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

    
    RS->>SOP: <<async>><br>invoke(input: RunInput, options?: RunnableConfig);
    activate SOP;
    SOP->>SOP: parse(text: string);
    SOP->>RS: text;
    deactivate SOP;
    
    RS->>User: text;
    deactivate RS;
```