# nodejs
0. Blocking và Non-blocking I/O?
0. Nodejs hay javascript xử lí promise như thế nào để chạy bất đồng bộ mà không dùng thread? `căng nha`
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
  
## Xử lí promise nè!!!
- Đầu tiên là mình phải hiểu răn là concurrency, răn là paralleism?
- Ok, đầu tiên là `concurrency` chỉ áp dụng khi có 2 hay nhiều hơn nhiệm vụ. Khi mà một app có khả năng thực thi 2 task một cách ảo lòi cùng lúc, chúng ta gọi nó là concurrent app. Mặc dù những task này nhìn thì giống như là đồng thời nhưng thực chất hông phải như thế. Chúng tận dụng tính năng CPU time-slicing của OS, nơi mà mỗi task chạy một phần của nó và rơi vào trạng thái chờ. Khi task đầu tiên rơi vào trạng thái chờ thì CPU sẽ thực hiện task thứ 2 để hoàn thành một phần của nó. OS dựa trên độ ưu tiên task, do vậy, việc assign CPU và những tài nguyên khác như memory, lần lượt cho từng task và cho chúng cơ hội để hoàn thành. Cho nên đối với end user thì giống như là chúng được thực hiện song song, nhưng mà đây gọi là `Concurrency`.
- Rồi tiếp đến là `Parallelism`. Thì dịch ra là xử lí song song, ở đây ko yêu cầu phải tồn tại 2 task. Nó thực sự chạy nhiều phần của một task hoặc là nhiều task, cùng một lúc sử dụng multi-core của CPU, bằng cách gán một core cho một task hoặc là sub-task. Xử lí song song yêu cầu phần cứng với multiple processing units. Với single core thì chỉ có thể chạy concurrency chớ còn parallel thì chịu.
- Ok vậy giờ thì 2 cái này khác nhau như thế nào, khá rõ ràng rồi cơ mà mình viết thêm chút :))
  - `Concurrency` là khi 2 task có thể start, run, và complete trong một overlapping time periods(khoảng thời gian chồng chéo nhau).  Còn `Parallelism` là khi các tasks chạy cùng lúc trên nhiều core.
  - `Concurrency` là thành phần của quá trình thực thi độc lập, trong khi `Paralleism` là việc thực thi đồng thời các tính toán.
  - `Concurrency` là xử lí nhiều thứ cùng một lúc (dealing with lots of things at once). Còn `Parallelism` là về việc làm nhiều thứ cùng lúc (do lots of things at once).

- Rồi giờ là promise (hay là việc xử lí bất đồng bộ trong JS). Bắt đầu là promise được thiết kế để giúp việc quản lí các tác vụ bất đồng bộ trong JS. Bản thân promise thì k cần dùng nhiều threads để thực hiện nó. Chúng là những objects mà về bản chất cung cấp bookkeeping(sổ sách kế toán - đại loại rứa) cho asynchronous operations - giữ những cờ trạng thái, giá trị kết quả và những cái lắng nghe sự kiện cho chuyển đổi trạng thái (listeners for a state transition). Tất cả những thứ này đều có thể dễ dàng được thực hiện bởi single threaded Javascript.
- Do đó, miễn là bạn có asynchronous operations, bạn có thể tận dụng những cái lợi ích từ promise và ko cần thread để implement mấy cái đó.
  
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

#### Sự phức tạp trong File I/O
- Không phải tất cả các loại I/O đều sử dụng những implementations ở trên. Mặc dù trong cùng OS platform, thì việc hỗ trợ những loại I/O cũng khác nhau và cũng rất phức tạp. Thông thường, network I/O được thực hiện theo các non-blocking sử dụng epoll, kqueue, event ports và IOCP, những File I/O lại phức tạp hơn nhiều. Trong những hệ thống cụ thể như Linux không hoàn toàn hỗ trợ việc truy cập file bất đồng bộ. Và có những giới hạn trong việc file system event notifications/signaling với kqueue trong MACOS systems. 
#### Sự phức tạp trong DNS
- Tương tự với file I/O, chức năng DNS được cung cấp trong Node cũng rất phức tạp. Vì DNS functions trong Node như dns.lookup truy cập vào file cấu hình hệ thống như nsswitch.conf, resolv.conf và /etc/hosts.
#### Giải pháp
- Do đó, một thread pool được sinh ra để hỗ trợ những I/O functions - mà không thể được giải quyết trực tiếp bằng hardware asynchronous I/O utils như epoll/kqueue/event ports/IOCP. 
- Và bây giờ thì chúng ta biết được là không phải tất cả I/O functions xảy ra trong thread pool. NodeJS sẽ làm việc mà nó có thể làm tốt nhất là xử lí hầu hết các I/O sử dụng non-blocking và asynchronous hardware I/O, nhưng với những loại I/O blocks hoặc phức tạp thì nó dùng thread pool.
  
### OK giờ gom tất cả lại nè
- Nói chung là trong thực tế thì rất khó để hỗ trợ tất cả các loại I/O (file I/O, network I/O, DNS, ...) trên tất cả các hệ điều hành khác nhau. Một vài I/O có thể được xử lí bằng các native hardware implementations trong khi vẫn giư được tính bất đồng bộ, và có một vài loại I/O phải được xử lí trong thread pool thì mới đảm bảo được tính chất bất đồng bộ.

> Một quan niệm sai lầm của các developer NodeJS là Node thực hiện tất cả các tác vụ I/O ở thread pool.
- Để quản lí qúa trình này trong việc hỗ trợ đa nền tảng I/O, cần có một lớp trừu tượng gói gọn tất cả các công việc phức tạp và liên nền tảng này lại, và chỉ thể hiện ra ngoài một API tổng quát ở tầng trên của Node.
> Và tadaaaa -> `libuv`
- Trên trang chủ của libuv:
  - libuv là một thư viện hỗ trợ đa nền tảng được viết cho NodeJS. Nó được thiết kế trên event-driven asynchronous I/O model.
  - Thư viện này cung cấp nhiều hơn một lớp trừu tượng đơn giản đối với những cơ chế I/O polling khác nhau: 'handles' và 'streams' cung cấp một lớp trừu tượng high-level cho sockets và những entities khác; cross-platform file I/O và threading functionality cũng được cung cấp, bên cạnh những thứ khác.
 - Rồi giờ sẽ xem cách mà libuv được xây dựng thông qua sơ đồ bên dưới:
 - <img src='https://miro.medium.com/max/1020/1*THT6G99kiIEPZ3Mm6N09Bw.png' />
 - Bây giờ thì chúng ta biết được là Event Demultiplexer không phải là một thực thể duy nhất mà là một collection của những I/O processing API trừu tượng bới libuv và cung cấp cho tầng trên của NodeJS. Libuv cung cấp toàn bộ `event loop` bao gồm cả cơ chế event queuing.
### Bây giờ mình tìm hiểu tiếp về Event Queue
- Event queue được xem là một data structure nơi mà tất cả các event được enqueued và xử lí bởi event loop một cách tuần tự cho dến khi queue rỗng. Nhưng cách nó xảy ra trong Node hoàn toàn khác với cách mà reactor pattern mô tả. 
> Có nhiều hơn một queue trong NodeJS để chứa các loại event khác nhau.
> Sau khi xử lí một phase và trước khi move sang phase tiếp theo, event loop sẽ xử lí 2 queue trung gian cho đến khi không còn một item nào còn lại trong những queue này.

- Có 4 loại queue dược xử lí bởi native libuv event loop.
  - Expired timers và interval queue - bao gồm những callback của các timers đến thời hạn `setTimeout` hoặc những hàm interval `setInterval`
  - IO Events Queue - Những I/O events đã hoàn thành
  - Immediates Queue - Callbacks được thêm vào bằng `setImmediate`
  - Close Handlers Queue - Bất kì `close` event handler (ví dụ như socket.on('close', ...).
 
 > Lưu ý à mặc dù những cái ở trên đều được mô tả đơn giản bằng từ 'Queue' nhưng chúng thực sự là những data structure khác nhau (ví dụ timer được lưu trong một min-heap).
 
 - Bên cạnh 4 queue chính, thì còn có 2 queue trung gian rất thú vị được xử lí bởi Node. Mặc dù những queue này không phải là một phần của libuv mà là của NodeJS: 
  - Next Ticks Queue - Callbacks được thêm vào từ `process.nextTick`
  - Other Microtasks Queue - Bao gồm những microtasks như resolved promise callbacks.
 ### Vậy nó hoạt động như thế nào?
 - Như bạn thấy trong sơ đồ ở dưới, Node bắt đầu Event Loop bằng cách check xem có timer nào đến thời hạn không (ở trong timers queue), và đi qua mỗi queue trong từng step trong khi vẫn duy trì một tham chiếu counter cho tất cả các item được xử lí. Xong khi xử lí xong close handlers queue, vòng lặp ẽ thoát. Xử lí mỗi queue trong event loop được xem như là một phase của nó.
 - <img src='https://miro.medium.com/max/951/1*aU5dr98pxTsZ4AMfnA6lNA.png' />
 - Còn về 2 queue trung gian được bôi đỏ, cho đến khi một phase hoàn thành thì event loop sẽ check 2 queue trung gian đó xem có tồn tại item nào không. Nếu có thì event loop sẽ lập tức xử lí nó cho đến khi 2 queue trung gian này trống rỗng thì thôi. Rồi sau đó event loop sẽ tiếp tục qua phase tiếp theo.
 > Ví dụ nhe: Event loop hiện tại xử lí immediates queue có 5 handlers cần xử lí. Trong khi đó, 2 handlers được thêm vào next tick queue. Một khi event loop hoàn thành 5 handlers trong immediates queue, event loop check là còn có 2 item trong next tick queue cần được xử lí trước khi move sang close handlers queue. Nó sẽ thực thi tất cả handlers trong next tick queue và sau đó chuyển sang xử lí close handlers queue.
 
 ### Next tick queue và Other microtasks queue
 - Next tick queue có ưu tiên cao hơn là Other Microtasks queue. Mặc dù chúng đều được xử lí giữa 2 phase của event loop khi libuv giao tiếp với những lớp cao hơn của NodeJS ở cuối mỗi phase. Để ý là next tick queue có màu đỏ đậm tượng trưng cho việc next tick queue phải được xử lí cho xong hết rồi mới tới xử lí resolved promises trong microtasks queue.
 > Ưu tiên next tick hơn resolved promises chỉ áp dụng với native JS promises cung cấp bởi V8. Nếu bạn sử dụng thư viện khác như `q` hay `bluebird`, thì bạn sẽ nhận được kết quả khác.
 > `q` và `bluebird` cũng có cách xử lí promise khác.
 
 - Điều này dẫn đến một vấn đề là IO starvation (bỏ đói IO), cái này thực ra có nghĩa là nếu mà cứ thêm vào next tick queue mãi thì event loop sẽ chẳng thể chuyển tới phase tiếp theo được, cái này hay làm block event loop nè.
 > Để ngăn chặn việc này thì người ta có dùng một maximum limit cho next tick queue có thể set thông qua `process.maxTickDepth`, nhưng mà ở phiên bản sau này thì bị remove òi.
 
 - Giờ nhìn lại kiến trúc của Node tí và xem libuv ở đâu
 - <img src='https://miro.medium.com/max/1120/1*-L-Atb5LI_j0nvcM0s43pA.jpeg' />
 
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
