# nodejs
0. Blocking và Non-blocking I/O?
1. Nodejs xử lí request như thế nào?
2. Single threaded Event loop hoạt động như thế nào?
3. Nếu register một promise thì node sẽ xử lí ntn?
4. Thread pool size trong node là bao nhiêu?
5. Tại sao event loop lại bị block? Làm sao để block nó, làm sao để unblock nó?
6. Cơ chế xử lí I/O tasks?

------------------------------------------------------------------------------
## Blocking và non-blocking I/O?
- I/O là các tác vụ tương tác với system's disk và network được hỗ trợ bởi libuv.
- Blocking là khi việc thực thi code JS trong Nodejs process phải đợi tới khi những tác vụ non-Javascript operations hoàn thành.
-> điều này dẫn đến event loop không thế tiếp tục thực thi Javascript trong khi có một blocking operation đang xảy ra.
-> Javascript trong Nodejs sẽ hoạt động vs hiệu năng kém nếu thực hiện các tác vụ CPU-intensive tasks hơn là chờ đợi các non-Javascript operations như I/O
Hầu hết các phương thức synchronous trong standard library của Nodejs đều sử dụng blocking operations.
- Tất cả I/O methods trong standard library Nodejs đều cung cấp asynchronous versions, được gọi là `NON_BLOCKING`
- `Concurrency và Throughput`:
  - Javascript thực thi trong Node là Single threaded, do đó xử lí đồng thời (concurrency)
  dựa vào khả năng của EventLoop để thực thi Javascript callback sau khi hoàn thành một công việc khác.
  - Bất kì đoạn code nào muốn chạy concurrent phải cho phép eventloop tiếp tục chạy khi các non-Javascript operations như I/O đang xảy ra.
  
## Nodejs xử lí request như thế nào?
- Nodejs hoạt động dựa trên một event-drive model với một Event Demultiplexer (trình phân tích sự kiện) và một Event Queue.
- Tất cả I/O requests sẽ tạo ra một sự kiện 'completion/failture' hoặc là bất kì một trigger nào khác, gọi là một Event.
- Những event này được xử lí dựa trên thuật toán sau:
  1. Event Demultiplexer nhận I/O requests và ủy thác các requests này tới phần cứng thích hợp.
  2. Một khi I/O request được xử lí (ví dụ như có thể đọc data từ một file, data từ socket), event demultiplexer sẽ thêm một callback handler đã được đăng kí cho một action cụ thể trong queue để xử lí.
  Những callback này được gọi là events và queue nơi event được thêm vào được gọi là `Event Queue`.
  3. Khi events có thể được xử lí trong event queue, chúng sẽ được thực thi tuần tự theo đúng thứ tự mà chúng được thêm vào đến khi queue rỗng.
  4. Nếu không có event nào trong Event Queue hoặc là Event Demultiplexer ko có pending request nào, thì chương trình sẽ kết thúc.
  Ngược lại thì process sẽ tiếp tục lại bước đầu tiên.
- Và program thực hiện tất cả cơ chế ở trên thì gọi là Event Loop.
- <img src='https://miro.medium.com/max/561/1*3fzASvL5gFrSC64hHKzQOQ.jpeg'/>

- Event Loop là single threaded và semi-infinite loop. Lí do mà nó gọi là semi-infinitite loop là nó chỉ thực khi kết thúc khi ko có công việc nào cần được hoàn thành, nghĩa là khi chương trình tắt thôi, chớ khi nào cũng có việc để làm cả.
- Sơ đồ ở trên là high-level overview về cách hoạt động của NodeJS và thể hiện những thành phần chính của một design patter gọi là Reactor Patter.
- Những chi tiết thì phức tạp hơn nhiều :(. 
  - Event demultiplexer không chỉ là một single component làm tất các cả tác vụ I/O trong tất cả cá nền tảng Ó.
  - Event queue không phải là một single queue như ở trên, là nơi mà tất các các thể loại event được queued và dequeued.
  - Và I/O không chỉ là loại event duy nhất được đưa vào queue.
### Event Demultiplexer
- Event Demultiplexer không tồn tại trong thực tế, nó chỉ là một khái niệm trừu tượng trong Reactor patter.
- Thực tế thì event demultiplexer được implemented trong nhiều hệ thống khác nhau với tên gọi là epoll trong Linux, kqueue trong BSD systems (MACOS), event ports trong Solaris, IOCP(Input Output Competion Port) trong Windowwns.
- Nodejs tận dụng những chức năng low-level non-blocking, asynchronous hardware I/O được cung cấp bởi những implementations ở trên.
 
 --------------------------------------------------------------------

- Event loop sau khi start thì sẽ chạy một vòng lặp vô hạn ở phase poll 
--> đợi request tới để xử lí
--> khi có request tới có 2 trường hợp: 
1. Nếu k có tác vụ nào I/O, network, async task thì sẽ xử lí và trả lại luôn (synchronous)
2. Nếu có task setTimeout, setImmediate -> thì sẽ chờ đến khi ko có task nào được trigger trong poll queue
  --> chuyển sang phase tiếp theo
  --> xử lí callback trong setImmediate ở phase check của event loop
  --> kết thúc loop
  --> nếu tới thời hạn của hàm setTimeout thì thực thi callback ở phase timers
  --> rồi quay lại phase poll để đợi request tiếp
3. Nếu có tác vụ I/O operations, hay tác vụ blocking I/O khác (crypto, ...) -> sẽ được xử lí trong threadpool (worker threads)
 - mỗi operation thì sẽ được đưa vào queue của threadpool, size của threadpool ở đây mặc định là 4,có thể điều chỉnh process.env.UV_THREADPOOL_SIZE 
 nên nó sẽ xử lí được 4 operations đồng thời, nếu như nhiều hơn thì nó thực hiện lần lượt từng chunks.
4. Nếu có các tác vụ non-blocking operations -> 
