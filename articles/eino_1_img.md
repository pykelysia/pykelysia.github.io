# Eino (1) _ 多模态 使用

从这篇文章开始，将开始记录 Golang 模型 `Eino` 的相关使用方式。

多模态的使用，在官方文档喝大部分搜索内容中几乎找不到具体的解决方案，即使问 ai 也无法获取准确完整**可用**的内容。所以我萌生了自己探索，看源码注释，实践总结，然后记录在自己的博客的想法。

## 核心

`Eino` 使用多模态的核心是位于 `package schema` 下的 `Message` 结构体中的属性 `MulitiContent` 以及相关注释。

```go
type Message struct {
    // ...

	// if MultiContent is not empty, use this instead of Content
	// if MultiContent is empty, use Content
	// Deprecated: Use UserInputMultiContent for user multimodal inputs and AssistantGenMultiContent for model multimodal outputs.
	MultiContent []ChatMessagePart `json:"multi_content,omitempty"`

	// UserInputMultiContent passes multimodal content provided by the user to the model.
	UserInputMultiContent []MessageInputPart `json:"user_input_multi_content,omitempty"`

	// AssistantGenMultiContent is for receiving multimodal output from the model.
	AssistantGenMultiContent []MessageOutputPart `json:"assistant_output_multi_content,omitempty"`

    // ...
}
```

从注释可以看到，只要属性 `MultiContent` 不为空就会使用该属性。但是从实践来看，可能已经无法优雅地就直接为该属性赋值(没有找到合适的函数，对应的结构体也被弃用)。

经过简单，不深入地探索，可以直接通过为属性 `UserInputMultiContent` 和 `AssistantGenMultiContent` 赋值来传递对应的多模态信息。

再简单追溯这两个属性的类型定义，可以发现几乎相差不多。

```go
type MessageInputPart struct {
	Type ChatMessagePartType `json:"type"`
	Text string `json:"text,omitempty"`Type is "image_url".
	Image *MessageInputImage `json:"image,omitempty"`
	Audio *MessageInputAudio `json:"audio,omitempty"`
	Video *MessageInputVideo `json:"video,omitempty"`
	File *MessageInputFile `json:"file,omitempty"`
}
type MessageOutputPart struct {
	Type ChatMessagePartType `json:"type"`
	Text string `json:"text,omitempty"`
	Image *MessageOutputImage `json:"image,omitempty"`
	Audio *MessageOutputAudio `json:"audio,omitempty"`
	Video *MessageOutputVideo `json:"video,omitempty"`
}
```

两者均为通过 `type` 属性来确定使用的是哪个具体的内容。

可选值有

```go
const (
	// ChatMessagePartTypeText means the part is a text.
	ChatMessagePartTypeText ChatMessagePartType = "text"
	// ChatMessagePartTypeImageURL means the part is an image url.
	ChatMessagePartTypeImageURL ChatMessagePartType = "image_url"
	// ChatMessagePartTypeAudioURL means the part is an audio url.
	ChatMessagePartTypeAudioURL ChatMessagePartType = "audio_url"
	// ChatMessagePartTypeVideoURL means the part is a video url.
	ChatMessagePartTypeVideoURL ChatMessagePartType = "video_url"
	// ChatMessagePartTypeFileURL means the part is a file url.
	ChatMessagePartTypeFileURL ChatMessagePartType = "file_url"
)
```

同时无论是 `input` 还是 `output` 都是一种由基类 `MessagePartCommon` 派生出来的派生类

且基本无属性添加。（ `Text` 是 `string` 类型，无特殊内容）

```go
type MessagePartCommon struct {
	// URL can either be a traditional URL or a special URL conforming to RFC-2397 (https://www.rfc-editor.org/rfc/rfc2397).
	// double check with model implementations for detailed instructions on how to use this.
	URL *string `json:"url,omitempty"`
	// Base64Data represents the binary data in Base64 encoded string format.
	Base64Data *string `json:"base64data,omitempty"`
	// MIMEType is the mime type , eg."image/png",""audio/wav" etc.
	MIMEType string `json:"mime_type,omitempty"`
	// Extra is used to store extra information.
	Extra map[string]any `json:"extra,omitempty"`
}
```

这里面的属性 `URL` 和 `Base64Data` 。 `Base64Data` 是用本地数据转码而成的，其有一个弊端就是所需的 `Token` 消耗量更多。



## 实践

 

最后贴出我从本地文件转为多模态信息的 `Lambda` 函数：

```go
func newLambda(ctx context.Context, input string) (output []*schema.Message, err error) {
	file, err := os.ReadFile(input)
	if err != nil {
		return
	}
	encodedImg := base64.StdEncoding.EncodeToString(file)
	output = append(output, &schema.Message{
		Role: schema.User,
		UserInputMultiContent: []schema.MessageInputPart{
			{
				Type: schema.ChatMessagePartTypeImageURL,
				Image: &schema.MessageInputImage{
					MessagePartCommon: schema.MessagePartCommon{
						Base64Data: &encodedImg,
						MIMEType:   `image/jpg`,
					},
				},
			},
		},
	})
	return
}
```

最后