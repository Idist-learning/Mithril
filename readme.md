
[Source](https://mithril.js.org/simple-application.html "Permalink to Simple application - Mithril.js")

# ứng dụng đơn giản - Mithril.js

Hãy thử phát triển một ứng dụng đơn giản mà bao gồm cả một số đặc điểm chính của các Single Page Application.

Một ví dụ về việc thực hiện các tương tác có thể tham khảo ở đây [flems: Simple Application][1]

Đầu tiên hãy tạo một điểm truy cập cho ứng dụng. Tạo một file `index.html`:    
    
```html
<!doctype html>
<html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <title>My Application</title>
    </head>
    <body>
        <script src="bin/app.js"></script>
    </body>
</html>
```

Dòng `<!doctype html>` cho biết đây là một trang sử dụng HTML 5. Thẻ meta `charset` đầu tiên cho biết kiểu giải mã của trang và thẻ meta `viewport` yêu cầu các trình duyệt trên mobile nên scale trang này như thế nào. thẻ `title` chứa nội dung được hiển thị trên thanh tab của browser cho ứng dụng này, và thẻ `script` cho biết đường dẫn tới file javascript đang điều khiển ứng dụng này là gì.

Chúng ta có thể tạo toàn bộ ứng dụng của mình trong một file javascript duy nhất, nhưng nếu làm như vậy thì việc điều hướng nền tảng code sau này sẽ rất khó khăn. Thay vào đó, hãy chỉ code thành các _module_, và ghép các module này thành một nhóm tại `bin\app.js`.

Có rất nhiều cách để cài đặt một công cụ quản lý, nhưng hầu hết chúng đều được phân phối thông qua NPM. Sự thật là hầu hết các thư viện và công cụ Javascript hiện đại bây giờ đều được phân phối bằng cách này, trong đó có cả Mithril. NPM là viết tắt của Node.js Package Manager. Để tải về NPM, [hãy cài đặt Node.js][2]; NPM được cài tự động thông qua nó. Khi cả Node.js và NPM được cài đặt, mở command line và thực hiện lệnh này:
    
```js
npm init -y
```

Nếu NPM được cài đặt thành công, một file `package.json` sẽ được tạo ra. File này sẽ chứa các khung meta-descrioption cơ bản về file. Hãy chỉnh sửa thông tin về project và author trong file này.

* * *

Để cài đặt Mithril, hãy làm theo phần giới thiệu trong trang [cài đặt][3]. khi bạn đã cài đặt xong Mithril vào một project cơ bản, chúng ta đã sẵn sàng để tạo một ứng dụng

Hãy bắt đầu bằng việc tạo một module để lưu trữ các trạng thái của chúng ta. Tạo một file `src/models/User.js`

```js
    // src/models/User.js
    var User = {
        list: []
    }
    
    module.exports = User
```  

Giờ thêm đoạn code để tải dữ liệu về thừ server. Để có thể giao tiếp với server, chúng ta có thể sử dụng tiện ích Mithril XHR, `m.request`. Đầu tiên, chúng ta thêm Mithril vào module:
   
```js
// src/models/User.js
var m = require("mithril")

var User = {
    list: []
}

module.exports = User
```

Tiếp theo chúng ta tạo một hàm sẽ kích hoạt được lời gọi XHR. Tạm gọi nó là `loadList`

```js
    // src/models/User.js
    var m = require("mithril")
    
    var User = {
        list: [],
        loadList: function() {
            // TODO: make XHR call
        }
    }
    
    module.exports = User
```  

Giờ chúng ta có thể thêm lời gọi hàm `m.request` để tạo ra một XHR request. Đối với bài hướng dẫn này, chúng ta sẽ sử dụng XHR để gọi tới [REM][4] API, một REST API giả lập để tạo ra các mẫu thử nhanh. API này sẽ trả lại một danh sách các user từ endpoint `GET https://rem-rest-api.herokuapp.com/api/users`. Hãy sử dụng `m.request` để tạo ra một XHR request và trả về dữ liệu cho chúng ta với endpoint đó. 

    
```js
    // src/models/User.js
    var m = require("mithril")
    
    var User = {
        list: [],
        loadList: function() {
            return m.request({
                method: "GET",
                url: "https://rem-rest-api.herokuapp.com/api/users",
                withCredentials: true,
            })
            .then(function(result) {
                User.list = result.data
            })
        },
    }
    
    module.exports = User
```    

Tùy chọn `method` là một [HTTP method][5]. Để truy xuất được dữ liệu từ server mà không gây ra các ảnh hưởng ngoại lệ trên server, chúng ta cần sử dụng phương thức `get`. `url` là địa của của API endpoint. Dòng `withCredentials: true` cho ta biết chúng ta đang sử dụng cookie (nó được yêu cầu cho REM API)

Lời gọi `m.request` trả về một Promise khi xử lý dữ liệu từ endpoint. Mặc định là Mithril giả định là phần nội dung của HTTP response có dạng Json và được tự động phân tích thành một Javascript object hoặc array. Hàm callback `.then` chạy khi XHR request hoàn tất. Trong trường hợp này, hàm callback chỉ định mảng `result.data` vào `User.list`.

Chú ý là chúng ta cũng có một mệnh đề `return` trong hàm `loadList`. Đây là bài thực hành  nhìn chung khá tốt khi làm việc với Promises,  cho phép chúng ra đăng ký nhiều lời gọi để chạy sau khi một XHR request hoàn tất.

Model đơn giản này cho thấy 2 thành phần: `User.list` (là một mảng các đối tượng user), và `User.loadList` (một phương thức để nhận dữ liệu từ server vào `User.list`)
* * *

Bây giờ hãy tạo một view module để chúng ta có thể hiển thị dữ liệu từ module User model của chúng ta.

Tạo một file tạm gọi là: `src/views/UserList.js`. Đầu tiên, hãy include Mithril và model của chúng ta, vì chúng ta sẽ cần cả 2 thứ:
    
```js
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
```

 Tiếp theo chúng ta tạo một component Mithril. Component này chỉ đơn giản là một đối tượng có một method `view`:   
    
```js
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        view: function() {
            // TODO add code here
        }
    }
```        
    

Mặc định thì views của Mithril đều được thể hiện bằng cách sử dụng [hyperscript][6]. Hyperscript cung cấp một cú pháp ngắn gọn mà có thể chèn vào các thẻ HTML phức tạp một cách dễ dàng hơn, và ngoài ra vì cú pháp của nó đơn giản chỉ là Javascript, nó có thể tận dụng các công cụ trong hệ sinh thái  của Javascript: ví dụ như [Babel][7], [JSX][8] (mở rộng cú pháp inline-HTML), [eslint][9] (gọn nhẹ), [uglifyjs][10] (rút gọn), [istanbul][11] (bảo mật code), [flow][12] (phân tích một cách cố định), etc.

Giờ hãy sử dụng hyperscript của Mithril để tạo danh sách các item. Hyperscript là cách tốt nhất để biểu diễn việc tạo một views cho Mithril. nhưng [JSX là một lựa chọn phổ biến khác mà bạn có thể tìm hiểu][8] khi bạn đã cảm thấy thoải mái hơn với những thứ cơ bản:
    
```js
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        view: function() {
            return m(".user-list")
        }
    }
```

Chuỗi `".user-list"` trong một selector CSS, và như bạn mong đợi, `.user-list` đại diện cho một class. Khi một tag không được chỉ định rõ ranhg, `div` được dùng một cách mặc định. Do đó chế độ xem này được coi như là ``.

Bây giờ,  hãy tham khảo danh sách các user từ model mà chúng ta đã tạo trước đó (`User.list`) để tự động lặp lại các dữ liệu:
    
```js
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        view: function() {
            return m(".user-list", User.list.map(function(user) {
                return m(".user-list-item", user.firstName + " " + user.lastName)
            }))
        }
    }
```
    

Vì `User.list` là một mảng trong Javascript, và bởi vì chế độ view của hyperscript cũng chỉ là Javascript, chúng ta hoàn toàn có thể lặp qua một array bằng cách sử dụng phương thức `.map`.Nó tạo ra một mảng của các vnode được đại diện thông qua các thẻ `div`, mỗi thẻ lại chứa tên của một user.

Có vấn đề, tất nhiên rồi, chúng ta chưa bao giờ gọi hàm `User.loadList`.  Vì thế, `User.list` vẫn là một array rỗng, và view này chỉ trả về một trang rỗng. Vì chúng ta muốn `User.loadList` được gọi khi chúng ta render component, chúng ta có thể tận dụng được ưu điểm của [vòng đời của các phương thức][13]:
    
```js
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        oninit: User.loadList,
        view: function() {
            return m(".user-list", User.list.map(function(user) {
                return m(".user-list-item", user.firstName + " " + user.lastName)
            }))
        }
    }
```
    

Chú ý là chúng ta đã thêm một phương thức `oninit` vào component, được tham chiếu tới `User.loadList`. Điều này có nghĩa là khi component được khởi tạo, User.loadLisst sẽ được gọi, kích hoạt một XHR request. Khi phía server trả về kết quả, `User.list` sẽ nhận được các giá trị của nó.

Cũng phải chú ý là chúng ta **không được** thực hiện việc `oninit: User.loadList()` (với các dấu ngoặc đơn ở cuối).  Điểm khác biệt ở đây là `oninit: User.loadList()` sẽ chỉ gọi hàm chỉ một lần và ngay lập tức, nhưng `oninit: User.loadList` chỉ gọi hàm khi component đó được render. Đây là điểm khác biệt khá quan trọng và cũng là lỗi chung cho các developer mới khi làm việc với Javascript: gọi hàm ngay lập tức có nghĩa là XHR request sẽ kích hoạt ngay sau khi source code tỉa hoàn tất, mặc dù component không bao giờ được render. Tương tự thế, nếu component được tái tạo lại, (thông qua việc điều hướng qua lại giữa các ứng dụng), các chức năng sẽ không được gọi lại như mong đợi.

* * *

Giờ hãy render view từ điểm truy cập file `src/index.js` chúng ta đã tạo trước đó:
    
```js
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    
    m.mount(document.body, UserList)
```
    
Lời gọi `m.mount` render component được chỉ định (`UserList`) vào một DOM element (`document.body`), nó xoá mọi DOM trước đó. Khi đó mở một file HTML trên browser sẽ hiển thị một danh sách tên người dùng.

* * *

Ngay bây giờ, danh sách trông khá là đơn giản vì chúng ta chưa định nghĩa bất kỳ style nào.

Ngày nay có rất nhiều tiêu chuẩn và thư viện hỗ trợ việc tổ chức một khung style cho ứng dụng. Một vài thứ như [Bootstrap][14] tuỳ chỉnh cho một tập hợp các cấu trúc HTML cụ thể và các tên class có ý nghĩa, trong đó nó cung cấp khả năng tích hợp các hệ thống với mức xung đột thấp, nhưng nhược điểm của có là tuỳ biến tương đối khó. Công cụ khác như [tachyons][15] cung cấp một số lượng lớn các class nguyên tố bằng việc tự mô tả với việc đặt tên class không cần phải tuân theo ngữ nghĩa. "CSS-in-JS" là một kiểu khác của hệ thống CSS đang ngày càng phổ biến, về cơ bản là mở rộng phạm vi của CSS thông qua các công cụ. Các thư việc CSS-in-JS có khả năng bảo trì cao vì được tối giản các vấn đề về kích thước, nhưng lại có độ phức tạp cao.

Bất kể là tiêu chuẩn hay thư viện CSS nào mà bạn chọn, có một nguyên tắc chung là tránh việc xếp tầng của CSS. Áp dụng với tutorial đơn giản này, chúng ta sẽ chỉ sử dụng CSS thuần với các tên lớp một cách rõ ràng, vì vậy để chúng tự cung cấp một cấu trúc nguyên tử như của Tachyons, và tên class ảnh hưởng tới nhau thông qua độ dài của tên các class đó. Các CSS cùng cấp độ có thể đủ áp dụng cho một project có độ phức tạp thấp. ( ví dụ như từ 3- 6 người trong một tháng và một vài giai đoạn của dự án)

Để thêm style, trước tiên hãy tạo một file `styles.css` và include nó vào file `index.html`:

```html
<!doctype html>
<html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <title>My Application</title>
        <link href="styles.css" rel="stylesheet" />
    </head>
    <body>
        <script src="bin/app.js"></script>
    </body>
</html>
```  

Giờ chúng ta có thể style lại `UserList` component:
    
```css
.user-list {list-style:none;margin:0 0 10px;padding:0;}
.user-list-item {background:#fafafa;border:1px solid #ddd;color:#333;display:block;margin:0 0 1px;padding:8px 15px;text-decoration:none;}
.user-list-item:hover {text-decoration:underline;}
```
    

phần CSS ở trên được viết theo quy tắc giữ tất cả các style trên một dòng, sắp xếp theo bảng chữ cái. Tiêu chuẩn này được thiết kế để tận dụng được tối đa phần màn hình cố định, và làm chúng đọc các selectors CSS dễ dàng hơn (vì chúng luôn nằm ở bên trái) và thuộc vào các nhóm logic của chúng, và chúng thực hiện các quy tắc có thể dự đoán và thống nhất cho từng bộ lọc.

Rõ ràng là bạn có thể sử dụng bất kỳ tiêu chuẩn về khoảng trống/ cách thụt lề mà bạn thích. Ví dụ trên chỉ là minh hoạ cho một quy ước không được sử dụng rộng rãi vì có những lý do lớn ở đằng sau nó, nhưng nó lại vượt qua được các quy ước về khoảng cách lớn về mặt mỹ thuật.

Giờ hãy load lại trình duyệt để hiển thị lại style cho các element. 
* * *

Giờ hãy thêm phần định tuyến vào ứng dụng.

Định tuyến có nghĩa gắn một khung màn hình vào một URL duy nhất, để tạo khả năng chuyển từ trang này sang trang khác. Mithril được thiết kế cho Single Page Applications, vì vậy "các trang" không cần thiết phải khác nhau về file HTML như cách truyền thống. thay vào đó, việc định tuyến trong Single Page Application chỉ giữ một file html duy nhất trong suốt vòng đời của nó, nhưng lại thay đổi các trạng thái của ứng dụng thông qua Javascript. Định tuyến phía client mang lại lợi ích  là việc tránh các lần nhấp nháy khi chuyển màn hình và có thể giảm được dữ liệu được gửi xuống từ server khi được dùng kết hợp với các web có kiến trúc định hướng dịch vụ (ví dụ một ứng dụng tải dữ liệu dạng Json thay cho việc tải các đoạn HTML được render trước đó).

Chúng ta có thể thêm phần định tuyến bằng việc thay đổi hàm gọi `m.mount` thành `m.route`:
    
```js
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    
    m.route(document.body, "/list", {
        "/list": UserList
    })
```
    
Lời gọi `m.route` chỉ định ứng dụng sẽ render vào `document.body`. Đối số `/list` là định tuyến mặc định. Nó có nghĩa là người dùng sẽ được redirect tới route này nếu họ truy cập vào một route không tồn tại. Object `{"/list": UserList}` khai báo một bản đồ các route tồn tại và các component mà mỗi route sẽ giải quyết.

Khi refresh lại trang trên trình duyệt sẽ được appent thêm `#!/list` vào URL để chỉ ra là route đang hoạt động. Vì route này render UserList, chúng ta vẫn xem được danh sách user trên màn hình như trước.

Đoạn `#!` được gọi là đoạn băm, và nó là chuỗi thường được sử dụng để triển khai việc định tuyến phía client. Có thể cấu hình lại nó thông qua [`m.route.prefix`][16]. Có vài phần config yêu cầu hỗ trợ từ các thay đổi phía server, vì vậy chúng ta sẽ tiếp thục sủ dụng mã băm này cho phần còn lại của bài tutorial.

* * *

Hãy thêm một route khác vào ứng dụng của chúng ta để thay đổi các user. Ddayau tiên hãy tạo một module là `views/UserForm.js`
    
```js
    // src/views/UserForm.js
    
    module.exports = {
        view: function() {
            // TODO implement view
        }
    }
```

 Sau đó chúng ta có thể `require` module mới này vào `src/index.js`.
    
```js
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    var UserForm = require("./views/UserForm")
    
    m.route(document.body, "/list", {
        "/list": UserList
    })
```
    
Và cuối cùng, chúng ta tạo một route trỏ tới nó:
    
```js
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    var UserForm = require("./views/UserForm")
    
    m.route(document.body, "/list", {
        "/list": UserList,
        "/edit/:id": UserForm,
    })
```
    

Chú ý route mới có một tham số `:id` trong nó. Đây là một tham số của route; bạn có thể nghĩ về nó như một thẻ không cố định; route `/edit/1` được phân giải về `UserForm` với `id` là `1`. `/edit/2` cũng được phân giải về `UserForm`, nhưng với `id` là `2`. Tương tự như thế.
Hãy triển khai component `UserForm`  để nó có thể đáp ứng các tham số của route:
    
```js
    // src/views/UserForm.js
    var m = require("mithril")
    
    module.exports = {
        view: function() {
            return m("form", [
                m("label.label", "First name"),
                m("input.input[type=text][placeholder=First name]"),
                m("label.label", "Last name"),
                m("input.input[placeholder=Last name]"),
                m("button.button[type=button]", "Save"),
            ])
        }
    }
```

 Giờ thêm một vài style vào `styles.css`:   
    
```css
    /* styles.css */
    body,.input,.button {font:normal 16px Verdana;margin:0;}
    
    .user-list {list-style:none;margin:0 0 10px;padding:0;}
    .user-list-item {background:#fafafa;border:1px solid #ddd;color:#333;display:block;margin:0 0 1px;padding:8px 15px;text-decoration:none;}
    .user-list-item:hover {text-decoration:underline;}
    
    .label {display:block;margin:0 0 5px;}
    .input {border:1px solid #ddd;border-radius:3px;box-sizing:border-box;display:block;margin:0 0 10px;padding:10px 15px;width:100%;}
    .button {background:#eee;border:1px solid #ddd;border-radius:3px;color:#333;display:inline-block;margin:0 0 10px;padding:10px 15px;text-decoration:none;}
    .button:hover {background:#e8e8e8;}
```

Bây giờ, component này chưa có gì để trả lời các sự kiện từ user. Hãy thêm một đoạn code vào model `User` của chúng ta trong file `src/models/User.js`. Đây là code chuẩn:
    
   ```js
 // src/models/User.js
    var m = require("mithril")
    
    var User = {
        list: [],
        loadList: function() {
            return m.request({
                method: "GET",
                url: "https://rem-rest-api.herokuapp.com/api/users",
                withCredentials: true,
            })
            .then(function(result) {
                User.list = result.data
            })
        },
    }
    
    module.exports = User
```
    
Hãy thêm code để cho phép ta tải được một user:    
    
```js
    // src/models/User.js
        var m = require("mithril")
        
        var User = {
            list: [],
            loadList: function() {
                return m.request({
                    method: "GET",
                    url: "https://rem-rest-api.herokuapp.com/api/users",
                    withCredentials: true,
                })
                .then(function(result) {
                    User.list = result.data
                })
            },
        
            current: {},
            load: function(id) {
                return m.request({
                    method: "GET",
                    url: "https://rem-rest-api.herokuapp.com/api/users/" + id,
                    withCredentials: true,
                })
                .then(function(result) {
                    User.current = result
                })
            }
        }
        
        module.exports = User
```

 Chú ý là chúng ta thêm thuộc tính `User.current`, và phương thức `User.load(id)` sẽ trả dữ liệu vào đó. Giờ chúng ta có thể điền vào view `UserForm` bằng việc sử dụng phương thức mới:
    
```js
// src/views/UserForm.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        oninit: function(vnode) {User.load(vnode.attrs.id)},
        view: function() {
            return m("form", [
                m("label.label", "First name"),
                m("input.input[type=text][placeholder=First name]", {value: User.current.firstName}),
                m("label.label", "Last name"),
                m("input.input[placeholder=Last name]", {value: User.current.lastName}),
                m("button.button[type=button]", "Save"),
            ])
        }
    }
```

Tương tự như các component `UserList`, `oninit` gọi tới `User.load()`. Hãy nhớ là chúng ta có một biến trên route được gọi `:id` trong route`"/edit/:id": UserForm`? Tham số trên route trở thành một thuộc tính vnode của component `UserList`,  vì thế việc điều hướng tới `/edit/1` sẽ tạo `vnode.attrs.id` có giá trị là `"1"`

Bây giờ, hãy sửa view `UserList` để chúng ta có thể điều hướng tới `UserForm`:
    
    
```js
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        oninit: User.loadList,
        view: function() {
            return m(".user-list", User.list.map(function(user) {
                return m("a.user-list-item", {href: "/edit/" + user.id, oncreate: m.route.link}, user.firstName + " " + user.lastName)
            }))
        }
    }
```
    

Ở đây chúng ta đổi `.user-list-item` thành `a.user-list-item`. Chúng ta thêm một `href` để tham chiếu tới route mà chúng ta muốn, và cuối cùng chúng ta thêm `oncreate: m.route.link`. Nó tạo ra link nhưng một link route (trái ngược như việc chỉ đối xử như một link bình thường). Điều này có nghĩa là khi click vào liên kết sẽ thay đổi một phần của URL ở phần băm phía sau `#!` (do đó nó có thể thay đổi route mà không cần load lại trang HTML hiện tại)
Nếu bạn làm mới trang trên trình duyệt, bạn đã có thể click vào một người và nhận được một form. Bạn cũng có thể nhấn vào nút back trên trình duyệt để quay lại từ form về danh sách các người dùng.

* * *

 Form này hiện chưa thể save khi bạn nhấn nút "Save". Hãy làm cho form này hoạt động:   
    
```js
// src/views/UserForm.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        oninit: function(vnode) {User.load(vnode.attrs.id)},
        view: function() {
            return m("form", {
                    onsubmit: function(e) {
                        e.preventDefault()
                        User.save()
                    }
                }, [
                m("label.label", "First name"),
                m("input.input[type=text][placeholder=First name]", {
                    oninput: m.withAttr("value", function(value) {User.current.firstName = value}),
                    value: User.current.firstName
                }),
                m("label.label", "Last name"),
                m("input.input[placeholder=Last name]", {
                    oninput: m.withAttr("value", function(value) {User.current.lastName = value}),
                    value: User.current.lastName
                }),
                m("button.button[type=submit]", "Save"),
            ])
        }
    }
```

Chúng ta đã thêm các sự kiện `oninput` vào cả 2 input, nó đặt vào các thuộc tính `User.current.firstName` và `User.current.lastName` khi người dùng nhập vào.

Ngoài ra, chúng ta khai báo một phương thức `User.save` có thể được gọi khi nút "Save" được nhấn. Hãy thực hiện phương thức này:
    
```js
    // src/models/User.js
    var m = require("mithril")
    
    var User = {
        list: [],
        loadList: function() {
            return m.request({
                method: "GET",
                url: "https://rem-rest-api.herokuapp.com/api/users",
                withCredentials: true,
            })
            .then(function(result) {
                User.list = result.data
            })
        },
    
        current: {},
        load: function(id) {
            return m.request({
                method: "GET",
                url: "https://rem-rest-api.herokuapp.com/api/users/" + id,
                withCredentials: true,
            })
            .then(function(result) {
                User.current = result
            })
        },
    
        save: function() {
            return m.request({
                method: "PUT",
                url: "https://rem-rest-api.herokuapp.com/api/users/" + User.current.id,
                data: User.current,
                withCredentials: true,
            })
        }
    }
    
    module.exports = User
    

```

Ở dưới cùng của phương thức `save`, chúng ta sử dụng phương thức `PUT` của HTTP để chỉ ra rằng chúng ta đang ghi lại dữ liệu lên server.

giờ hãy thử edit tên của một user trong ứng dụng. Khi bạn lưu một thay đổi, bạn có thể thấy được sự thay đổi được phản hồi ngay trên danh sách user.

* * *

Bây giờ chúng ta chỉ có thể điều hướng quay trở lại danh sách user thông qua nút back của trình duyệt. Ý tưởng ở đây là chúng ta càn có một menu - hoặc cái gì đó tương tự, một layout 
mà chúng ta có thể đặt các phần thử cho giao diện người dùng toàn cục.

Hãy tạo một file `src/views/Layout.js`:    
    
```js
    // src/views/Layout.js
    var m = require("mithril")
    
    module.exports = {
        view: function(vnode) {
            return m("main.layout", [
                m("nav.menu", [
                    m("a[href='https://mithril.js.org/list']", {oncreate: m.route.link}, "Users")
                ]),
                m("section", vnode.children)
            ])
        }
    }
```
    
Component này khá đơn giản, nó có một thẻ `<nav>` với một link tới danh sách các user. Tương tự như những gì chúng ta thực hiện với các link `/edit`, link này sử dụng `m.route.link` để kích hoạt các hoạt động định tuyến trên link.

Chú ý là cũng có thành phần `<section>` với các `vnode.children` như các thành phần con. `vnode` là một tham chiếu tới vnode đại diện cho một thể hiện của component Layout ( ví dụ như vnode trả về qua lời gọi `m(Layout)`). Do đó, `vnode.children` ánh xạ tới bất kì thành phần con là của vnode.

Hãy thêm một số style:    
    
```css
 /* styles.css */
    body,.input,.button {font:normal 16px Verdana;margin:0;}
    
    .layout {margin:10px auto;max-width:1000px;}
    .menu {margin:0 0 30px;}
    
    .user-list {list-style:none;margin:0 0 10px;padding:0;}
    .user-list-item {background:#fafafa;border:1px solid #ddd;color:#333;display:block;margin:0 0 1px;padding:8px 15px;text-decoration:none;}
    .user-list-item:hover {text-decoration:underline;}
    
    .label {display:block;margin:0 0 5px;}
    .input {border:1px solid #ddd;border-radius:3px;box-sizing:border-box;display:block;margin:0 0 10px;padding:10px 15px;width:100%;}
    .button {background:#eee;border:1px solid #ddd;border-radius:3px;color:#333;display:inline-block;margin:0 0 10px;padding:10px 15px;text-decoration:none;}
    .button:hover {background:#e8e8e8;}
```    

Hãy thay đổi định tuyến trong `src/index.js` để thêm Layout của chúng ta vào mix:    
    
```js
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    var UserForm = require("./views/UserForm")
    var Layout = require("./views/Layout")
    
    m.route(document.body, "/list", {
        "/list": {
            render: function() {
                return m(Layout, m(UserList))
            }
        },
        "/edit/:id": {
            render: function(vnode) {
                return m(Layout, m(UserForm, vnode.attrs))
            }
        },
    })
```

Chúng tôi đã thay thế các component bằng một [RouteResolver][17] (về cơ bản, mỗi đối tượng ứng với một phương thức `render` ). Phương thức `render` có thể được viết lại bằng cùng một cách mà các view component thông thường hay thực hiện, thông qua lời gọi `m()`.

Có vài điều thú vị cần chú ý là làm cách nào các component có thể sử dụng thay cho một chuỗi selector trong một lời gọi `m()`. Ở đây, trong route `list`, chúng ta có `m(Layout, m(UserList))` Điều này có nghĩa là có một vnode gốc được đại diện cho `Layout`, nó có một vnode `UserList` được xem như là con của nó.

Trong route `/edit/:id`, cũng có một đối số `vnode` mang tham số của route vào component `UserForm`. vì vậy nếu URL là `/edit/1`, sau đó `vnode.attrs` trong trường hợp này là `{id: 1}`, và `m(UserForm, vnode.attrs)` tương đương với `m(UserForm, {id: 1})`. Mã JSX tương tự sẽ là `<UserForm id={vnode.attrs.id} />`.

Refresh lại troang trên trình duyệt và bây giờ bạn có thể điều hướng một cách toàn cục trên mọi trang của ứng dụng.
* * *

Tóm lại tutorial này,

Trong tutorial này, chúng ta đã đi qua một quá trình tạo ra một ứng dụng cực kì đơn giản, nơi mà chúng ta có thể hiển thị danh sách user từ server và sửa chúng một cách riêng rẽ. Tương tự với một bài tập bổ sung, hãy cố gắng tạo thêm và xoá chúng khỏi ứng dụng của bạn.

Nếu bạn muốn xem thêm các ví dụ khác của Mithril, xem qua trang [các ví dụ][18]. Nếu bạn có câu hỏi, hãy ghé qua [Mithril chat room][19].
* * *

License: MIT. © Leo Horie.

[1]: https://flems.io/#0=N4IgzgpgNhDGAuEAmIBcICGAHLA6AVmCADQgBmAljEagNqgB2GAthGiAKqQBOAsgPZJoBIqVj8GiSewBuGbgAIuERQF4FwADoMFuhVAph4qBbQC6xbXv38MSADKHjCsgFcGCChIAUASg1W1nrcEPCu3DrMuCEAjq4QRt5aOkGprPAAFoImmiAA4gCiACq5limp1uFQOSAZ8PBYYKgA9M0hzAC0IUYd2BS4GSr8ANau2HjizM19za48YKWBFXoA7hSZAMIhQpIUGFBNCvDc8WXLAL6+S0G4mRAM3m4e8F4P3a5Q8P7Jy9bK3LgDEYFOp3p9cEgMPAMNdrJdrucytdYOEQpITMBEdcoLYkCYnp4fBQkN9YcFQuFItEIHEEvAkmS0qEsniFLlCiUSIyglUanUGk1Wu0unTelh+oNuCMxjhcJNpuLZvNmrkFABqBTEs6-XRrTbbe4vfaHY6nbnw8o3O4PAkvHxgr4BS3Lf5y1GGkEKB3mq6WrEMa5gDAyCD49yEh6k53ksIRBRRWLxRI-HXx5nZNkgAAKHE52p1vMz-MaLTaEE63XgYolQ1G4zl-CmMzmKjAKpA6qUPDd3DR8FwWu51kh0JMrpRvcN+d+eoyW2Qhr2BxMpog07hvrh2nOIERjBYbHQ-0cRhEJBA4kkhtk8i7KhP8E9Kd0EgoDHWY+7OLsD+nMgoEArGGzyvH4TrLCEsaRN4uS4C23AdEC8ClHeAJIbgzDYI84Z2g88FRqmXoUnGzAwZgcE8IhTgdOs5YocAGQhGQNTNMg6ztp28EDkgxAKBIsAhFCobxtE-CuIggJvsMiIKFxlDcEYAByB6dqqqoalxUAYEpB6bhUlx6bo5zbruxD7qw7D-AAYvw3BRIQ56XlI8A3oo1m2cwT7XK+77OLaoEyAwggQN8rrfkg3iBcFuBQscYDcb4-rWP+gHARGYHPkEkGUvGZFkB59FDhUEhgK4ABGzAfi4OGgSF4GERUEC4FgIQhpIAAiEBkBgHz0oZDV-N2QYhn4RWpMZ0bjbxtBjbluRaWVwgLdAKG5FZFAKY+TCsLkvjrhUpG5G+WDiQODAnfAtDwAAnlgECqIgAAe8BmLQWBabAEBZFAQjcKo62bQo20QGYhWTb8PkXSYUSzgAgvU3BkXIUDxCh-k+Mj8Shd2E59rg8k6awnqYxAlz7TqJOfioPZ4wT8DKTt4MbuTQSHSAy1QICGCLVAq0gPY2lbQeu0s9YbPHadEuXe9GCfd9v2qALwLA6DJD1QNkPidDuBwwjSP7Kjavow8JPY9TuOGlzhMQMTBuk3ts3JXbVMAhbkhW-TwtM3oZOzWzZXifAEi4AH9QSFdt33aVFXrKrvG5AAysGEAi9yZj9RNO57iAwPsAL11if2DliBIzmuQo+eF15lopUB1UgRjQVCARFTZSRZGYW+XMF+JKEzd7uhs0wMgYfcrh947ehszCauZQNjFdSYADkzRIUvosQx4gmINrUriU1BgMMMk9GfHnDzLts3pxvg9kZAEYoVFQhyhkVBIGi-XWOnCImdnufoPWYuF5S7XnQAmQuTUWpdQoI9bwS8ADES9fTgP3t4JA-AUSsHdmVQQ10z6rycGDawuQCFGFyBibkaJfppVwhlWabdoKV3ErxUix4nC+E-j7BE04SFsXgM0VAxJyHqyyvcah9d0pPzqnPVuxFGEYB7vAFh3h3J2V4lImKCMwAcPNNw7cvhTLmUPCAOUYBRDAKvNIdAOCkB4LOhdYgIdA4SA0PldEQU7L7AUAARgAGxYEegoAAaioSETAADcmFuAAHM3wmAAEwAAYAkTW0N3KuwAomxIYKgbxyTAk9SDpEjAj0OhrCQJkXJiTqkBPCRNUeDBXAaCyXExJCg2kAGZ8l1O0Gk+CVFgTACQh0Iw10YCoCCgwCAxSYmtPaT47pWA7BIDfNE1AiSekMAoioAZVZaKeWAGVWWwxol7wYHieB3UrkYHCTg7g1DvEBIUGAfgBgkAKHgUgL54TxA4m4KgeBHSgXhJWWAGW11UBlRxLAYYMzsnrPmY8x64SllfNWagAAHE87xABWWpT0qxCHENwKErwJkSGmfU-pwz9moCyCGRQwACUdCJbZUlEhUDuF+ofSlvStkcw0KC8FkLoWwpaTktpbS8XIvqVLDQdyHlPJeW8j5XykC3Nsr9LodgKBzFQB02pODSlgAoAAL3RQqnZRqQWGGFVCjBYr5DwslQs2pqKVkMDWXk7F0rwnlMqXkxJABSTZTiw46EOcc05YlzkAogPGjV9yVC5KVa84kqrvmWoQiSlZeqDXIt+bZAFQKOk2rBVpCFb4eUdHtTCuFcy2neuRe69FTafG+uZaykluFyVTNDaHIOOT6UqHlVGs5FyIAYsnZOupu4LDsykjQegOcDzsEqpkbgVBzxVHYMWQUsxzonIbFMddjEqAAAFvG4Cvb45op7N2cyATdO67AHLnDMOcIAA
[2]: https://nodejs.org/en/
[3]: https://mithril.js.org/installation.html
[4]: http://rem-rest-api.herokuapp.com/
[5]: https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods
[6]: https://mithril.js.org/hyperscript.html
[7]: https://mithril.js.org/es6.html
[8]: https://mithril.js.org/jsx.html
[9]: http://eslint.org/
[10]: https://github.com/mishoo/UglifyJS2
[11]: https://github.com/gotwarlost/istanbul
[12]: https://flowtype.org/
[13]: https://mithril.js.org/lifecycle-methods.html
[14]: http://getbootstrap.com/
[15]: http://tachyons.io/
[16]: https://mithril.js.org/route.html#mrouteprefix
[17]: https://mithril.js.org/route.html#routeresolver
[18]: https://mithril.js.org/examples.html
[19]: https://gitter.im/MithrilJS/mithril.js
