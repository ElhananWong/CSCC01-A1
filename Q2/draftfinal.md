```mermaid
---
title: Output Parser System
---

classDiagram
direction TB

class Runnable~I,O~ {
  <<abstract>>
  + invoke(input: I, options?: Partial<CallOptions>) Promise~O~*
  + pipe~NewRunOutput~(coerceable: RunnableLike~O, NewRunOutput~) Runnable
  + batch(inputs: I[], options?: Partial~CallOptions~ | Partial~CallOptions~[], batchOptions?: RunnableBatchOptions) Promise~O[] | Error[]~
  + stream(input: I, options?: Partial~CallOptions~) Promise~IterableReadableStream~O~~
  + *transform(generator: AsyncGenerator~I~, options: Partial~CallOptions~) AsyncGenerator~O~
}

class BaseLLMOutputParser~T~ {
  <<abstract>>
  + parseResult(generations: Generation[] | ChatGeneration[], callbacks?: Callbacks) Promise~T~*
  + parseResultWithPrompt(generations: Generation[] | ChatGeneration[], _promptL BasePromptValueInterface, callbacks?: Callbacks) Promise~T~
  + invoke(input: string | BaseMessage, options?: RunnableConfig) Promise~T~
  # _baseMessageToString(message: BaseMessage) string
  # _baseMessageContentToString(content: ContentBlock[]) string
}

class BaseOutputParser~T~ {
  <<abstract>>
  + parseResult(generations: Generation[] | ChatGeneration[], callbacks?: Callbacks) Promise~T~
  + parse(text: string, callbacks?: Callbacks) Promise~T~*
  + parseWithPrompt(text: string, prompt, callbacks?) Promise~T~
  + getFormatInstructions(options?: FormatInstructionsOptions) string*
}

class BaseTransformOutputParser~T~ {
  <<abstract>>
  + *_transform(inputGenerator: AsyncGenerator~string | BaseMessage~) AsyncGenerator~T~
  + *transform(inputGenerator: AsyncGenerator~string | BaseMessage~, options: BaseCallbackConfig) AsyncGenerator~T~
}

class StringOutputParser {
  + parse(text: string) Promise~string~
  + getFormatInstructions() string
  # _baseMessageContentToString(content: ContentBlock[]) string
  # _messageContentToString(content: ContentBlock) string
}

class BaseCumulativeTransformOutputParser~T~ {
  <<abstract>>
  # diff : boolean = false
  + parsePartialResult(generations: Generation[] | ChatGeneration[]) Promise~T | undefined~
  # _diff(prev: any | undefined, next: any) any
  + getFormatInstructions() string
  # _transform(inputGenerator: AsyncGenerator~string | BaseMessage~) AsyncGenerator~T~
}

class JsonOutputParser~T~ {
  + parse(text: string) Promise~T~
  + parsePartialResult(generations) Promise~T | undefined~
  + getFormatInstructions() string
  # _diff(prev: unknown | undefined, next: unknown) Operation[] | undefined
}

class XMLOutputParser {
  + tags?: string[]
  + parse(text: string) Promise~XMLResult~
  + parsePartialResult(generations: ChatGeneration[] | Generation[]) Promise~XMLResult | undefined~
  + getFormatInstructions() string
  # _diff(prev: unknown | undefined, next: unknown) Operation[] | undefined
}

Runnable <|-- BaseLLMOutputParser
BaseLLMOutputParser <|-- BaseOutputParser
BaseOutputParser <|-- BaseTransformOutputParser
BaseTransformOutputParser <|-- StringOutputParser
BaseTransformOutputParser <|-- BaseCumulativeTransformOutputParser
BaseCumulativeTransformOutputParser <|-- JsonOutputParser
BaseCumulativeTransformOutputParser <|-- XMLOutputParser
```
changes:
- added BaseCumulativeTransformOutputParser, JsonOutputParser, and XMLOutputParser classes

what prompted change:
- explored these other classes