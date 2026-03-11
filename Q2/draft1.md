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
}

class BaseLLMOutputParser~T~ {
  <<abstract>>
  + parseResult(generations: Generation[], callbacks?: Callbacks) Promise~T~*
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
}

Runnable <|-- BaseLLMOutputParser
BaseLLMOutputParser <|-- BaseOutputParser
BaseOutputParser <|-- BaseTransformOutputParser
BaseTransformOutputParser <|-- StringOutputParser

```
changes:
- initial draft

what prompted change:
- initial peruse of output parser classes