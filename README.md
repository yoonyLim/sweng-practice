```mermaid
flowchart TD
    Start([시작: 프레임 업데이트]) --> CheckLeft{왼손이 먼저 감지되었는가?}

    %% Condition 1: Left Hand is Primary
    CheckLeft -- Yes --> SetLeftRef[기준점 설정: 왼손 손목<br>is_left_hand_wrist_based = True]
    SetLeftRef --> CalcLeft[왼손 좌표 계산<br>(기준: 왼손 손목)]
    CalcLeft --> CheckRightSub{오른손도 감지되었는가?<br>(Condition 1-1)}

    %% Condition 1-1 True
    CheckRightSub -- Yes --> CalcRightRelLeft[오른손 좌표 계산<br>(기준: 왼손 손목)]
    
    %% Condition 1-1 False
    CheckRightSub -- No --> KeepRightPrev[오른손 좌표 유지<br>(이전 프레임 값 or 0)]

    %% Condition 2: Right Hand is Primary (Left was False)
    CheckLeft -- No --> CheckRight{오른손이 감지되었는가?<br>(Condition 2)}

    %% Condition 2 True
    CheckRight -- Yes --> SetRightRef[기준점 설정: 오른손 손목<br>is_left_hand_wrist_based = False]
    SetRightRef --> CalcRight[오른손 좌표 계산<br>(기준: 오른손 손목)]
    CalcRight --> CheckLeftSub{왼손도 감지되었는가?<br>(Condition 2-1)}

    %% Condition 2-1 True
    CheckLeftSub -- Yes --> CalcLeftRelRight[왼손 좌표 계산<br>(기준: 오른손 손목)]

    %% Condition 2-1 False
    CheckLeftSub -- No --> KeepLeftPrev[왼손 좌표 유지<br>(이전 프레임 값 or 0)]

    %% Condition 3: No Hands
    CheckRight -- No --> NoHands[좌표 전송 안 함<br>(Condition 3)]

    %% End nodes
    CalcRightRelLeft --> SendData([데이터 전송])
    KeepRightPrev --> SendData
    CalcLeftRelRight --> SendData
    KeepLeftPrev --> SendData
    NoHands --> End([종료 / 다음 프레임 대기])
    SendData --> End

    %% Styling
    style Start fill:#f9f,stroke:#333,stroke-width:2px
    style SendData fill:#bbf,stroke:#333,stroke-width:2px
    style End fill:#f9f,stroke:#333,stroke-width:2px
    style SetLeftRef fill:#dfd,stroke:#333
    style SetRightRef fill:#dfd,stroke:#333
```
