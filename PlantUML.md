# Biểu Đồ UML (PlantUML)

Các biểu đồ dưới đây mô tả hệ thống Trắc Nghiệm Online bằng cú pháp PlantUML.

## 1. Sơ đồ lớp (Class Diagram)

```plantuml
@startuml
skinparam classAttributeIconSize 0

package "Server" {
    class Server {
        - HOST: str = "0.0.0.0"
        - PORT: int = 9999
        - DB_CONNECTION: Connection
        + __init__()
        + start_server()
        + handle_client(client_socket, address)
        + close_server()
    }

    class DatabaseManager {
        - connection: Connection
        + __init__(host, user, password, db)
        + connect()
        + disconnect()
        + get_or_create_user(name): Tuple[int, int]
        + get_questions(limit=10): List[Dict]
        + update_score(user_id, score)
        + get_leaderboard(limit=5): List[Dict]
    }

    class QuestionHandler {
        + format_question(index, question): str
        + check_answer(answer, correct_answer): bool
        + format_result(user_name, score): str
    }
}

package "Client" {
    class QuizClient {
        - HOST: str = "127.0.0.1"
        - PORT: int = 9999
        - socket: Socket
        - buffer: str
        - score: int
        - player_name: str
        - time_remaining: int
        - timer_running: bool
        - expecting_question: bool
        + __init__(master)
        + setup_ui()
        + start_listening()
        + send_answer(answer)
        + process_data_from_buffer()
        + show_overlay(message, color, sub_message)
        + start_timer()
        + update_timer()
        + time_up()
        + close_connection()
    }

    class GUI {
        - master: TkinterRoot
        - frame_main: Frame
        - frame_question: Frame
        - label_question: Label
        - radio_buttons: List[RadioButton]
        - button_submit: Button
        - label_timer: Label
        - label_score: Label
        + __init__(master)
        + create_widgets()
        + set_question(question_text)
        + clear_selection()
        + disable_inputs()
        + enable_inputs()
        + update_score(score)
        + update_timer(time)
    }

    class ScoreHistory {
        - history_file: str
        + __init__(filename="score_history.json")
        + load_history(): List[Dict]
        + save_score(player_name, score)
        + get_statistics(): Dict
    }
}

database "Database" {
    class Users {
        + id: int <<PK>>
        + name: varchar
        + score: int
    }

    class Topics {
        + id: int <<PK>>
        + name: varchar
    }

    class Questions {
        + id: int <<PK>>
        + topic_id: int <<FK>>
        + question: text
        + option_a: varchar
        + option_b: varchar
        + option_c: varchar
        + option_d: varchar
        + correct_option: char
    }
}

Server --> DatabaseManager
Server --> QuestionHandler
QuizClient --> GUI
QuizClient --> ScoreHistory
DatabaseManager --> Users
DatabaseManager --> Questions
DatabaseManager --> Topics
Questions --> Topics

@enduml
```

## 2. Sơ đồ tuần tự (Sequence Diagram)

```plantuml
@startuml
actor "User" as User
participant "Client" as Client
participant "Server" as Server
participant "Database" as DB

== Kết nối và xác thực ==
User -> Client: Khởi động ứng dụng
Client -> Server: Kết nối
Server --> Client: Gửi thông báo chào mừng
Client -> User: Hiển thị dialog nhập tên
User -> Client: Nhập tên người chơi
Client -> Server: Gửi tên người chơi
Server -> DB: Kiểm tra/tạo người chơi
DB --> Server: Trả về thông tin người chơi
Server -> Client: Gửi thông báo bắt đầu game
Client -> User: Hiển thị yêu cầu bấm 0
User -> Client: Bấm phím 0
Client -> Server: Gửi tín hiệu bắt đầu

== Vòng lặp trò chơi ==
Server -> DB: Lấy câu hỏi ngẫu nhiên
DB --> Server: Trả về danh sách câu hỏi
loop 10 lần
    Server -> Client: Gửi câu hỏi
    Client -> User: Hiển thị câu hỏi và bắt đầu đếm ngược

    alt Người dùng chọn đáp án đúng thời gian
        User -> Client: Chọn đáp án
        Client -> Server: Gửi đáp án
    else Hết thời gian
        Client -> Client: Tự động gửi đáp án trống
        Client -> Server: Gửi đáp án trống
    end

    Server -> Server: Kiểm tra đáp án

    alt Đáp án đúng
        Server -> Client: Gửi thông báo đúng
        Client -> User: Hiển thị overlay "CHÍNH XÁC!"
        Client -> Client: Cập nhật điểm số
    else Đáp án sai
        Server -> Client: Gửi thông báo sai và đáp án đúng
        Client -> User: Hiển thị overlay "SAI RỒI!"
        Client -> User: Hiển thị đáp án đúng
    end
end

== Kết thúc ==
Server -> DB: Cập nhật điểm số người chơi
Server -> DB: Lấy bảng xếp hạng
DB --> Server: Trả về bảng xếp hạng
Server -> Client: Gửi kết quả cuối cùng
Client -> Client: Lưu điểm vào score_history.json
Client -> User: Hiển thị kết quả và bảng xếp hạng
Client -> Client: Đóng kết nối sau 20 giây

@enduml
```

## 3. Sơ đồ hoạt động (Activity Diagram)

```plantuml
@startuml
start
:Khởi động Server;
:Lắng nghe kết nối từ Client;

fork
    :Client kết nối;
    if (Kết nối thành công?) then (yes)
        :Nhập tên người chơi;
        :Gửi tên tới Server;
    else (no)
        :Hiển thị thông báo lỗi;
        stop
    endif
fork again
    :Server chấp nhận kết nối;
    :Tạo thread xử lý client mới;
    :Gửi lời chào và yêu cầu tên;
    :Nhận tên người chơi;
    :Kiểm tra/tạo người chơi trong DB;
    :Yêu cầu tín hiệu bắt đầu;
end fork

:Client gửi tín hiệu bắt đầu;
:Server lấy câu hỏi từ database;

repeat
    :Server gửi câu hỏi;
    :Client hiển thị câu hỏi;
    :Client bắt đầu đếm ngược;

    if (Người dùng chọn đáp án trước khi hết giờ?) then (yes)
        :Client gửi đáp án tới server;
    else (no)
        :Client hiển thị thông báo hết giờ;
        :Client tự động gửi đáp án;
    endif

    :Server kiểm tra đáp án;

    if (Đáp án đúng?) then (yes)
        :Server gửi thông báo đúng;
        :Client cập nhật điểm số;
        :Client hiển thị thông báo đúng;
    else (no)
        :Server gửi thông báo sai;
        :Client hiển thị thông báo sai;
    endif

    :Tự động chuyển câu hỏi tiếp theo;
repeat while (Còn câu hỏi?) is (yes)
->no;

:Server cập nhật điểm vào DB;
:Server lấy bảng xếp hạng;
:Server gửi kết quả cuối cùng;
:Client hiển thị kết quả cuối;
:Client lưu điểm vào file JSON;
:Client hiển thị bảng xếp hạng;
:Đóng kết nối;

stop
@enduml
```

## 4. Sơ đồ thành phần (Component Diagram)

```plantuml
@startuml
package "Server Side" {
    [Server] as server
    [DatabaseManager] as dbManager
    [QuestionHandler] as qHandler
    database "MySQL" as mysql

    server -- dbManager
    server -- qHandler
    dbManager -- mysql
}

package "Client Side" {
    [QuizClient] as client
    [GUI Components] as gui
    [Score History] as history
    [Socket Handler] as socket

    client -- gui
    client -- history
    client -- socket
}

cloud {
    [Network]
}

server .. Network
socket .. Network

@enduml
```

## 5. Sơ đồ triển khai (Deployment Diagram)

```plantuml
@startuml
node "Server Machine" {
    artifact "server.py" as serverApp
    artifact "database_manager.py" as dbManager
    artifact "question_handler.py" as qHandler

    database "MySQL Database" {
        [Users Table]
        [Topics Table]
        [Questions Table]
    }

    serverApp --> dbManager
    serverApp --> qHandler
    dbManager --> [MySQL Database]
}

node "Client Machine" {
    artifact "client.py" as clientApp
    artifact "gui_components.py" as gui
    artifact "score_history.json" as history

    clientApp --> gui
    clientApp --> history
}

cloud {
    interface "TCP/IP" as tcpip
}

serverApp -- tcpip
clientApp -- tcpip

@enduml
```

## 6. Sơ đồ trạng thái (State Diagram)

```plantuml
@startuml
[*] --> NotConnected

NotConnected --> Connecting : Khởi động ứng dụng
Connecting --> Connected : Kết nối thành công
Connecting --> Failed : Lỗi kết nối
Failed --> [*] : Hiển thị lỗi và thoát

Connected --> WaitingForUsername : Nhận thông báo chào mừng
WaitingForUsername --> ReadyToStart : Gửi tên người chơi
ReadyToStart --> WaitingForQuestions : Gửi tín hiệu bắt đầu

state GameLoop {
    WaitingForQuestions --> DisplayingQuestion : Nhận câu hỏi

    DisplayingQuestion --> WaitingForAnswer : Hiển thị câu hỏi
    DisplayingQuestion --> TimerRunning : Bắt đầu đếm ngược

    TimerRunning --> TimerExpired : Hết thời gian
    TimerRunning --> WaitingForResult : Người dùng chọn đáp án
    TimerExpired --> WaitingForResult : Tự động gửi đáp án

    WaitingForResult --> DisplayingResult : Nhận kết quả
    DisplayingResult --> WaitingForQuestions : Còn câu hỏi
}

GameLoop --> GameCompleted : Hết câu hỏi

GameCompleted --> ShowingFinalResult : Nhận kết quả cuối cùng
ShowingFinalResult --> SavingScore : Hiển thị kết quả
SavingScore --> ShowingLeaderboard : Lưu điểm
ShowingLeaderboard --> Disconnected : Sau 20 giây

Disconnected --> [*]

@enduml
```

## 7. Sơ đồ usecase (Use Case Diagram)

```plantuml
@startuml
left to right direction

actor "Người chơi" as Player
actor "Quản trị viên" as Admin

rectangle "Hệ thống Trắc Nghiệm Online" {
    usecase "Đăng nhập với tên" as UC1
    usecase "Chơi trò chơi" as UC2
    usecase "Trả lời câu hỏi" as UC3
    usecase "Xem kết quả" as UC4
    usecase "Xem bảng xếp hạng" as UC5

    usecase "Quản lý người chơi" as UC6
    usecase "Quản lý câu hỏi" as UC7
    usecase "Thêm/sửa/xóa câu hỏi" as UC8
    usecase "Phân loại câu hỏi theo chủ đề" as UC9
    usecase "Sao lưu/phục hồi dữ liệu" as UC10
}

Player --> UC1
Player --> UC2
UC2 ..> UC3 : include
UC2 ..> UC4 : include
Player --> UC5

Admin --> UC6
Admin --> UC7
UC7 ..> UC8 : include
UC7 ..> UC9 : include
Admin --> UC10

@enduml
```

## 8. Sơ đồ gói (Package Diagram)

```plantuml
@startuml
package "Server" {
    package "Core" {
        [Server]
        [ClientHandler]
        [ThreadManager]
    }

    package "Database" {
        [DatabaseManager]
        [QueryBuilder]
        [ConnectionPool]
    }

    package "Game Logic" {
        [QuestionHandler]
        [ScoreCalculator]
        [LeaderboardManager]
    }
}

package "Client" {
    package "Network" {
        [SocketHandler]
        [DataReceiver]
        [DataSender]
    }

    package "UI" {
        [MainWindow]
        [QuestionUI]
        [ResultUI]
        [LeaderboardUI]
    }

    package "Logic" {
        [GameController]
        [TimerManager]
        [ScoreTracker]
    }

    package "Storage" {
        [LocalStorage]
        [ScoreHistory]
    }
}

[Server] ..> [ClientHandler]
[Server] ..> [ThreadManager]
[ClientHandler] ..> [DatabaseManager]
[ClientHandler] ..> [QuestionHandler]
[DatabaseManager] ..> [QueryBuilder]
[DatabaseManager] ..> [ConnectionPool]
[QuestionHandler] ..> [ScoreCalculator]
[ScoreCalculator] ..> [LeaderboardManager]

[GameController] ..> [SocketHandler]
[SocketHandler] ..> [DataReceiver]
[SocketHandler] ..> [DataSender]
[GameController] ..> [QuestionUI]
[GameController] ..> [ResultUI]
[GameController] ..> [TimerManager]
[GameController] ..> [ScoreTracker]
[ScoreTracker] ..> [LocalStorage]
[LocalStorage] ..> [ScoreHistory]
[GameController] ..> [LeaderboardUI]
@enduml
```
