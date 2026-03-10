```mermaid
sequenceDiagram
    actor User;
    participant RS as RunnableSequence;
    participant CPT as ChatPromptTemplate;
    participant BCM as BaseChatModel;
    participant SOP as StringOutputParser;

    User->>RS: chain.invoke({ topic: "ice cream" });
    activate RS;
    
    RS->>CPT: <<async>><br>invoke(input: RunInput, options?: RunnableConfig);
    activate CPT;
    CPT->>CPT: <<async>><br>formatMessages(values: TypedPromptInputValues<RunInput>);
    CPT->>RS: resultMessages [Messages];

    RS->>BCM: <<aync>><br>invoke(input: RunInput, options?: RunnableConfig)
    activate BCM
    BCM->>BCM: <<async>><br>_generatemessages(BaseMessage[],<br>options: this["ParsedCallOptions"],<br>runManager?: CallbackManagerForLLMRun)
    BCM->>RS: AIMessage
    deactivate BCM
    
    RS->>SOP: <<async>><br>invoke(input: RunInput, options?: RunnableConfig);
    activate SOP;
    SOP->>SOP: parse(text: string);
    SOP->>RS: text;
    deactivate SOP;
    
    RS->>User: text;
    deactivate RS;
```
changes:
- added BaseChatModel and its method calls

what prompted change:
- looked at ChatOpenAI class (libs/providers/langchain-openai/src/chat_models/index.ts)