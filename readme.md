
[Source](https://mithril.js.org/simple-application.html "Permalink to Simple application - Mithril.js")

# Simple application - Mithril.js
# ứng dụng đơn giản - Mithril.js

Let's develop a simple application that covers some of the major aspects of Single Page Applications
Hãy thử phát triển một ứng dụng đơn giản mà bao gồm cả một số đặc điểm chính của các Single Page Application.

An interactive running example can be seen here [flems: Simple Application][1]
Một ví dụ về việc thực hiện các tương tác có thể tham khảo ở đây [flems: Simple Application][1]

First let's create an entry point for the application. Create a file `index.html`:
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
    

The `<!doctype html>` line indicates this is an HTML 5 document. The first `charset` meta tag indicates the encoding of the document and the `viewport` meta tag dictates how mobile browsers should scale the page. The `title` tag contains the text to be displayed on the browser tab for this application, and the `script` tag indicates what is the path to the Javascript file that controls the application.

Dòng `<!doctype html>` cho biết đây là một trang sử dụng HTML 5. Thẻ meta `charset` đầu tiên cho biết kiểu giải mã của trang và thẻ meta `viewport` yêu cầu các trình duyệt trên mobile nên scale trang này như thế nào. thẻ `title` chứa nội dung được hiển thị trên thanh tab của browser cho ứng dụng này, và thẻ `script` cho biết đường dẫn tới file javascript đang điều khiển ứng dụng này là gì.

We could create the entire application in a single Javascript file, but doing so would make it difficult to navigate the codebase later on. Instead, let's split the code into _modules_, and assemble these modules into a _bundle_ `bin/app.js`.
Chúng ta có thể tạo toàn bộ ứng dụng của mình trong một file javascript duy nhất, nhưng nếu làm như vậy thì việc điều hướng nền tảng code sau này sẽ rất khó khăn. Thay vào đó, hãy chỉ code thành các _module_, và ghép các module này thành một nhóm tại `bin\app.js`.

There are many ways to setup a bundler tool, but most are distributed via NPM. In fact, most modern Javascript libraries and tools are distributed that way, including Mithril. NPM stands for Node.js Package Manager. To download NPM, [install Node.js][2]; NPM is installed automatically with it. Once you have Node.js and NPM installed, open the command line and run this command:
Có rất nhiều cách để cài đặt một công cụ quản lý, nhưng hầu hết chúng đều được phân phối thông qua NPM. Sự thật là hầu hết các thư viện và công cụ Javascript hiện đại bây giờ đều được phân phối bằng cách này, trong đó có cả Mithril. NPM là viết tắt của Node.js Package Manager. Để tải về NPM, [hãy cài đặt Node.js][2]; NPM được cài tự động thông qua nó. Khi cả Node.js và NPM được cài đặt, mở command line và thực hiện lệnh này:
    
```js
npm init -y
```

If NPM is installed correctly, a file `package.json` will be created. This file will contain a skeleton project meta-description file. Feel free to edit the project and author information in this file.
Nếu NPM được cài đặt thành công, một file `package.json` sẽ được tạo ra. File này sẽ chứa các khung meta-descrioption cơ bản về file. Hãy chỉnh sửa thông tin về project và author trong file này.

* * *

To install Mithril, follow the instructions in the [installation][3] page. Once you have a project skeleton with Mithril installed, we are ready to create the application.
Để cài đặt Mithril, hãy làm theo phần giới thiệu trong trang [cài đặt][3]. khi bạn đã cài đặt xong Mithril vào một project cơ bản, chúng ta đã sẵn sàng để tạo một ứng dụng

Let's start by creating a module to store our state. Let's create a file called `src/models/User.js`
Hãy bắt đầu bằng việc tạo một module để lưu trữ các trạng thái của chúng ta. Tạo một file `src/models/User.js`

```js
    // src/models/User.js
    var User = {
        list: []
    }
    
    module.exports = User
```  

Now let's add code to load some data from a server. To communicate with a server, we can use Mithril's XHR utility, `m.request`. First, we include Mithril in the module:
Giờ thêm đoạn code để tải dữ liệu về thừ server. Để có thể giao tiếp với server, chúng ta có thể sử dụng tiện ích Mithril XHR, `m.request`. Đầu tiên, chúng ta thêm Mithril vào module:
   
```js
// src/models/User.js
var m = require("mithril")

var User = {
    list: []
}

module.exports = User
```
Next we create a function that will trigger an XHR call. Let's call it `loadList`
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

Then we can add an `m.request` call to make an XHR request. For this tutorial, we'll make XHR calls to the [REM][4] API, a mock REST API designed for rapid prototyping. This API returns a list of users from the `GET https://rem-rest-api.herokuapp.com/api/users` endpoint. Let's use `m.request` to make an XHR request and populate our data with the response of that endpoint.
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

The `method` option is an [HTTP method][5]. To retrieve data from the server without causing side-effects on the server, we need to use the `GET` method. The `url` is the address for the API endpoint. The `withCredentials: true` line indicates that we're using cookies (which is a requirement for the REM API).
Tùy chọn `method` là một [HTTP method][5]. Để truy xuất được dữ liệu từ server mà không gây ra các ảnh hưởng ngoại lệ trên server, chúng ta cần sử dụng phương thức `get`. `url` là địa của của API endpoint. Dòng `withCredentials: true` cho ta biết chúng ta đang sử dụng cookie (nó được yêu cầu cho REM API)

The `m.request` call returns a Promise that resolves to the data from the endpoint. By default, Mithril assumes a HTTP response body are in JSON format and automatically parses it into a Javascript object or array. The `.then` callback runs when the XHR request completes. In this case, the callback assigns the `result.data` array to `User.list`.
Lời gọi `m.request` trả về một Promise khi xử lý dữ liệu từ endpoint. Mặc định là Mithril giả định là phần nội dung của HTTP response có dạng Json và được tự động phân tích thành một Javascript object hoặc array. Hàm callback `.then` chạy khi XHR request hoàn tất. Trong trường hợp này, hàm callback chỉ định mảng `result.data` vào `User.list`.

Notice we also have a `return` statement in `loadList`. This is a general good practice when working with Promises, which allows us to register more callbacks to run after the completion of the XHR request.
Chú ý là chúng ta cũng có một mệnh đề `return` trong hàm `loadList`. Đây là bài thực hành  nhìn chung khá tốt khi làm việc với Promises,  cho phép chúng ra đăng ký nhiều lời gọi để chạy sau khi một XHR request hoàn tất.
This simple model exposes two members: `User.list` (an array of user objects), and `User.loadList` (a method that populates `User.list` with server data).
Model đơn giản này cho thấy 2 thành phần: `User.list` (là một mảng các đối tượng user), và `User.loadList` (một phương thức để nhận dữ liệu từ server vào `User.list`)
* * *

Now, let's create a view module so that we can display data from our User model module.
Bây giờ hãy tạo một view module để chúng ta có thể hiển thị dữ liệu từ module User model của chúng ta.
Create a file called `src/views/UserList.js`. First, let's include Mithril and our model, since we'll need to use both:
Tạo một file tạm gọi là: `src/views/UserList.js`. Đầu tiên, hãy include Mithril và model của chúng ta, vì chúng ta sẽ cần cả 2 thứ:
    
```js
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
```
    

Next, let's create a Mithril component. A component is simply an object that has a `view` method:
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
    

By default, Mithril views are described using [hyperscript][6]. Hyperscript offers a terse syntax that can be indented more naturally than HTML for complex tags, and in addition, since its syntax is simply Javascript, it's possible to leverage a lot of Javascript tooling ecosystem: for example [Babel][7], [JSX][8] (inline-HTML syntax extension), [eslint][9] (linting), [uglifyjs][10] (minification), [istanbul][11] (code coverage), [flow][12] (static type analysis), etc.
Mặc định thì views của Mithril đều được thể hiện bằng cách sử dụng [hyperscript][6]. Hyperscript cung cấp một cú pháp ngắn gọn mà có thể chèn vào các thẻ HTML phức tạp một cách dễ dàng hơn, và ngoài ra vì cú pháp của nó đơn giản chỉ là Javascript, nó có thể tận dụng các công cụ trong hệ sinh thái  của Javascript: ví dụ như [Babel][7], [JSX][8] (mở rộng cú pháp inline-HTML), [eslint][9] (gọn nhẹ), [uglifyjs][10] (rút gọn), [istanbul][11] (bảo mật code), [flow][12] (phân tích một cách cố định), etc.
Let's use Mithril hyperscript to create a list of items. Hyperscript is the most idiomatic way of writing Mithril views, but [JSX is another popular alternative that you could explore][8] once you're more comfortable with the basics:
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
    

The `".user-list"` string is a CSS selector, and as you would expect, `.user-list` represents a class. When a tag is not specified, `div` is the default. So this view is equivalent to `
`.
Chuỗi `".user-list"` trong một selector CSS, và như bạn mong đợi, `.user-list` đại diện cho một class. Khi một tag không được chỉ định rõ ranhg, `div` được dùng một cách mặc định. Do đó chế độ xem này được coi như là ``.

Now, let's reference the list of users from the model we created earlier (`User.list`) to dynamically loop through data:
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
    

Since `User.list` is a Javascript array, and since hyperscript views are just Javascript, we can loop through the array using the `.map` method. This creates an array of vnodes that represents a list of `div`s, each containing the name of a user.
Vì `User.list` là một mảng trong Javascript, và bởi vì chế độ view của hyperscript cũng chỉ là Javascript, chúng ta hoàn toàn có thể lặp qua một array bằng cách sử dụng phương thức `.map`.Nó tạo ra một mảng của các vnode được đại diện thông qua các thẻ `div`, mỗi thẻ lại chứa tên của một user.
The problem, of course, is that we never called the `User.loadList` function. Therefore, `User.list` is still an empty array, and thus this view would render a blank page. Since we want `User.loadList` to be called when we render this component, we can take advantage of component [lifecycle methods][13]:
    
    
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
    

Notice that we added an `oninit` method to the component, which references `User.loadList`. This means that when the component initializes, User.loadList will be called, triggering an XHR request. When the server returns a response, `User.list` gets populated.
Chú ý là chúng ta đã thêm một phương thức `oninit` vào component, được tham chiếu tới `User.loadList`. Điều này có nghĩa là khi component được khởi tạo, User.loadLisst sẽ được gọi, kích hoạt một XHR request. Khi phía server trả về kết quả, `User.list` sẽ nhận được các giá trị của nó.

Also notice we **didn't** do `oninit: User.loadList()` (with parentheses at the end). The difference is that `oninit: User.loadList()` calls the function once and immediately, but `oninit: User.loadList` only calls that function when the component renders. This is an important difference and a common pitfall for developers new to javascript: calling the function immediately means that the XHR request will fire as soon as the source code is evaluated, even if the component never renders. Also, if the component is ever recreated (through navigating back and forth through the application), the function won't be called again as expected.
Cũng phải chú ý là chúng ta **không được** thực hiện việc `oninit: User.loadList()` (với các dấu ngoặc đơn ở cuối).  Điểm khác biệt ở đây là `oninit: User.loadList()` sẽ chỉ gọi hàm chỉ một lần và ngay lập tức, nhưng `oninit: User.loadList` chỉ gọi hàm khi component đó được render. Đây là điểm khác biệt khá quan trọng và cũng là lỗi chung cho các developer mới khi làm việc với Javascript: gọi hàm ngay lập tức có nghĩa là XHR request sẽ kích hoạt ngay sau khi source code tỉa hoàn tất, mặc dù component không bao giờ được render. Tương tự thế, nếu component được tái tạo lại, (thông qua việc điều hướng qua lại giữa các ứng dụng), các chức năng sẽ không được gọi lại như mong đợi.


* * *

Let's render the view from the entry point file `src/index.js` we created earlier:
Giờ hãy render view từ điểm truy cập file `src/index.js` chúng ta đã tạo trước đó:
    
```js
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    
    m.mount(document.body, UserList)
```
    

The `m.mount` call renders the specified component (`UserList`) into a DOM element (`document.body`), erasing any DOM that was there previously. Opening the HTML file in a browser should now display a list of person names.
Lời gọi `m.mount` render component được chỉ định (`UserList`) vào một DOM element (`document.body`), nó xoá mọi DOM trước đó. Khi đó mở một file HTML trên browser sẽ hiển thị một danh sách tên người dùng.

* * *

Right now, the list looks rather plain because we have not defined any styles.
Ngay bây giờ, danh sách trông khá là đơn giản vì chúng ta chưa định nghĩa bất kỳ style nào.

There are many similar conventions and libraries that help organize application styles nowadays. Some, like [Bootstrap][14] dictate a specific set of HTML structures and semantically meaningful class names, which has the upside of providing low cognitive dissonance, but the downside of making customization more difficult. Others, like [Tachyons][15] provide a large number of self-describing, atomic class names at the cost of making the class names themselves non-semantic. "CSS-in-JS" is another type of CSS system that is growing in popularity, which basically consists of scoping CSS via transpilation tooling. CSS-in-JS libraries achieve maintainability by reducing the size of the problem space, but come at the cost of having high complexity.
Ngày nay có rất nhiều tiêu chuẩn và thư viện hỗ trợ việc tổ chức một khung style cho ứng dụng. Một vài thứ như [Bootstrap][14] tuỳ chỉnh cho một tập hợp các cấu trúc HTML cụ thể và các tên class có ý nghĩa, trong đó nó cung cấp khả năng tích hợp các hệ thống với mức xung đột thấp, nhưng nhược điểm của có là tuỳ biến tương đối khó. Công cụ khác như [tachyons][15] cung cấp một số lượng lớn các class nguyên tố bằng việc tự mô tả với việc đặt tên class không cần phải tuân theo ngữ nghĩa. "CSS-in-JS" là một kiểu khác của hệ thống CSS đang ngày càng phổ biến, về cơ bản là mở rộng phạm vi của CSS thông qua các công cụ. Các thư việc CSS-in-JS có khả năng bảo trì cao vì được tối giản các vấn đề về kích thước, nhưng lại có độ phức tạp cao.


Regardless of what CSS convention/library you choose, a good rule of thumb is to avoid the cascading aspect of CSS. To keep this tutorial simple, we'll just use plain CSS with overly explicit class names, so that the styles themselves provide the atomicity of Tachyons, and class name collisions are made unlikely through the verbosity of the class names. Plain CSS can be sufficient for low-complexity projects (e.g. 3 to 6 man-months of initial implementation time and few project phases).
Bất kể là tiêu chuẩn hay thư viện CSS nào mà bạn chọn, có một nguyên tắc chung là tránh việc xếp tầng của CSS. Áp dụng với tutorial đơn giản này, chúng ta sẽ chỉ sử dụng CSS thuần với các tên lớp một cách rõ ràng, vì vậy để chúng tự cung cấp một cấu trúc nguyên tử như của Tachyons, và tên class ảnh hưởng tới nhau thông qua độ dài của tên các class đó. Các CSS cùng cấp độ có thể đủ áp dụng cho một project có độ phức tạp thấp. ( ví dụ như từ 3- 6 người trong một tháng và một vài giai đoạn của dự án)

To add styles, let's first create a file called `styles.css` and include it in the `index.html` file:
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

Now we can style the `UserList` component:
Giờ chúng ta có thể style lại `UserList` component:
    
```css
.user-list {list-style:none;margin:0 0 10px;padding:0;}
.user-list-item {background:#fafafa;border:1px solid #ddd;color:#333;display:block;margin:0 0 1px;padding:8px 15px;text-decoration:none;}
.user-list-item:hover {text-decoration:underline;}
```
    

The CSS above is written using a convention of keeping all styles for a rule in a single line, in alphabetical order. This convention is designed to take maximum advantage of screen real estate, and makes it easier to scan the CSS selectors (since they are always on the left side) and their logical grouping, and it enforces predictable and uniform placement of CSS rules for each selector.
phần CSS ở trên được viết theo quy tắc giữ tất cả các style trên một dòng, sắp xếp theo bảng chữ cái. Tiêu chuẩn này được thiết kế để tận dụng được tối đa phần màn hình cố định, và làm chúng đọc các selectors CSS dễ dàng hơn (vì chúng luôn nằm ở bên trái) và thuộc vào các nhóm logic của chúng, và chúng thực hiện các quy tắc có thể dự đoán và thống nhất cho từng bộ lọc.

Obviously you can use whatever spacing/indentation convention you prefer. The example above is just an illustration of a not-so-widespread convention that has strong rationales behind it, but deviate from the more widespread cosmetic-oriented spacing conventions.
Rõ ràng là bạn có thể sử dụng bất kỳ tiêu chuẩn về khoảng trống/ cách thụt lề mà bạn thích. Ví dụ trên chỉ là minh hoạ cho một quy ước không được sử dụng rộng rãi vì có những lý do lớn ở đằng sau nó, nhưng nó lại vượt qua được các quy ước về khoảng cách lớn về mặt mỹ thuật.

Reloading the browser window now should display some styled elements.
Giờ hãy load lại trình duyệt để hiển thị lại style cho các element. 
* * *

Let's add routing to our application.
Giờ hãy thêm phần định tuyến vào ứng dụng.

Routing means binding a screen to a unique URL, to create the ability to go from one "page" to another. Mithril is designed for Single Page Applications, so these "pages" aren't necessarily different HTML files in the traditional sense of the word. Instead, routing in Single Page Applications retains the same HTML file throughout its lifetime, but changes the state of the application via Javascript. Client side routing has the benefit of avoiding flashes of blank screen between page transitions, and can reduce the amount of data being sent down from the server when used in conjunction with an web service oriented architecture (i.e. an application that downloads data as JSON instead of downloading pre-rendered chunks of verbose HTML).
Định tuyến có nghĩa gắn một khung màn hình vào một URL duy nhất, để tạo khả năng chuyển từ trang này sang trang khác. Mithril được thiết kế cho Single Page Applications, vì vậy "các trang" không cần thiết phải khác nhau về file HTML như cách truyền thống. thay vào đó, việc định tuyến trong Single Page Application chỉ giữ một file html duy nhất trong suốt vòng đời của nó, nhưng lại thay đổi các trạng thái của ứng dụng thông qua Javascript. Định tuyến phía client mang lại lợi ích  là việc tránh các lần nhấp nháy khi chuyển màn hình và có thể giảm được dữ liệu được gửi xuống từ server khi được dùng kết hợp với các web có kiến trúc định hướng dịch vụ (ví dụ một ứng dụng tải dữ liệu dạng Json thay cho việc tải các đoạn HTML được render trước đó).

We can add routing by changing the `m.mount` call to a `m.route` call:
Chúng ta có thể thêm phần định tuyến bằng việc thay đổi hàm gọi `m.mount` thành `m.route`:
    
```js
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    
    m.route(document.body, "/list", {
        "/list": UserList
    })
```
    
The `m.route` call specifies that the application will be rendered into `document.body`. The `"/list"` argument is the default route. That means the user will be redirected to that route if they land in a route that does not exist. The `{"/list": UserList}` object declares a map of existing routes, and what components each route resolves to.
Lời gọi `m.route` chỉ định ứng dụng sẽ render vào `document.body`. Đối số `/list` là định tuyến mặc định. Nó có nghĩa là người dùng sẽ được redirect tới route này nếu họ truy cập vào một route không tồn tại. Object `{"/list": UserList}` khai báo một bản đồ các route tồn tại và các component mà mỗi route sẽ giải quyết.

Refreshing the page in the browser should now append `#!/list` to the URL to indicate that routing is working. Since that route render UserList, we should still see the list of people on screen as before.
Khi refresh lại trang trên trình duyệt sẽ được appent thêm `#!/list` vào URL để chỉ ra là route đang hoạt động. Vì route này render UserList, chúng ta vẫn xem được danh sách user trên màn hình như trước.

The `#!` snippet is known as a hashbang, and it's a commonly used string for implementing client-side routing. It's possible to configure this string it via [`m.route.prefix`][16]. Some configurations require supporting server-side changes, so we'll just continue using the hashbang for the rest of this tutorial.
Đoạn `#!`

* * *

Let's add another route to our application for editing users. First let's create a module called `views/UserForm.js`
    
    
```js
    // src/views/UserForm.js
    
    module.exports = {
        view: function() {
            // TODO implement view
        }
    }
```
    

Then we can `require` this new module from `src/index.js`
    
    
```js
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    var UserForm = require("./views/UserForm")
    
    m.route(document.body, "/list", {
        "/list": UserList
    })
```
    

And finally, we can create a route that references it:
    
    
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
    

Notice that the new route has a `:id` in it. This is a route parameter; you can think of it as a wild card; the route `/edit/1` would resolve to `UserForm` with an `id` of `"1"`. `/edit/2` would also resolve to `UserForm`, but with an `id` of `"2"`. And so on.

Let's implement the `UserForm` component so that it can respond to those route parameters:
    
    
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
    

And let's add some styles to `styles.css`:
    
    
    /* styles.css */
    body,.input,.button {font:normal 16px Verdana;margin:0;}
    
    .user-list {list-style:none;margin:0 0 10px;padding:0;}
    .user-list-item {background:#fafafa;border:1px solid #ddd;color:#333;display:block;margin:0 0 1px;padding:8px 15px;text-decoration:none;}
    .user-list-item:hover {text-decoration:underline;}
    
    .label {display:block;margin:0 0 5px;}
    .input {border:1px solid #ddd;border-radius:3px;box-sizing:border-box;display:block;margin:0 0 10px;padding:10px 15px;width:100%;}
    .button {background:#eee;border:1px solid #ddd;border-radius:3px;color:#333;display:inline-block;margin:0 0 10px;padding:10px 15px;text-decoration:none;}
    .button:hover {background:#e8e8e8;}
    

Right now, this component does nothing to respond to user events. Let's add some code to our `User` model in `src/models/User.js`. This is how the code is right now:
    
    
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
    

Let's add code to allow us to load a single user
    
    
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
    

Notice we added a `User.current` property, and a `User.load(id)` method which populates that property. We can now populate the `UserForm` view using this new method:
    
    
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
    

Similar to the `UserList` component, `oninit` calls `User.load()`. Remember we had a route parameter called `:id` on the `"/edit/:id": UserForm` route? The route parameter becomes an attribute of the `UserForm` component's vnode, so routing to `/edit/1` would make `vnode.attrs.id` have a value of `"1"`.

Now, let's modify the `UserList` view so that we can navigate from there to a `UserForm`:
    
    
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
    

Here we changed `.user-list-item` to `a.user-list-item`. We added an `href` that references the route we want, and finally we added `oncreate: m.route.link`. This makes the link behave like a routed link (as opposed to merely behaving like a regular link). What this means is that clicking the link would change the part of URL that comes after the hashbang `#!` (thus changing the route without unloading the current HTML page)

If you refresh the page in the browser, you should now be able to click on a person and be taken to a form. You should also be able to press the back button in the browser to go back from the form to the list of people.

* * *

The form itself still doesn't save when you press "Save". Let's make this form work:
    
    
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
    

We added `oninput` events to both inputs, that set the `User.current.firstName` and `User.current.lastName` properties when a user types.

In addition, we declared that a `User.save` method should be called when the "Save" button is pressed. Let's implement that method:
    
    
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
    

In the `save` method at the bottom, we used the `PUT` HTTP method to indicate that we are upserting data to the server.

Now try editing the name of a user in the application. Once you save a change, you should be able to see the change reflected in the list of users.

* * *

Currently, we're only able to navigate back to the user list via the browser back button. Ideally, we would like to have a menu - or more generically, a layout where we can put global UI elements

Let's create a file `src/views/Layout.js`:
    
    
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
    

This component is fairly straightforward, it has a `
` with a link to the list of users. Similar to what we did to the `/edit` links, this link uses `m.route.link` to activate routing behavior in the link.

Notice there's also a `
` element with `vnode.children` as children. `vnode` is a reference to the vnode that represents an instance of the Layout component (i.e. the vnode returned by a `m(Layout)` call). Therefore, `vnode.children` refer to any children of that vnode.

Let's add some styles:
    
    
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
    

Let's change the router in `src/index.js` to add our layout into the mix:
    
    
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
    

We replaced each component with a [RouteResolver][17] (basically, an object with a `render` method). The `render` methods can be written in the same way as regular component views would be, by nesting `m()` calls.

The interesting thing to pay attention to is how components can be used instead of a selector string in a `m()` call. Here, in the `/list` route, we have `m(Layout, m(UserList))`. This means there's a root vnode that represents an instance of `Layout`, which has a `UserList` vnode as its only child.

In the `/edit/:id` route, there's also a `vnode` argument that carries the route parameters into the `UserForm` component. So if the URL is `/edit/1`, then `vnode.attrs` in this case is `{id: 1}`, and this `m(UserForm, vnode.attrs)` is equivalent to `m(UserForm, {id: 1})`. The equivalent JSX code would be ``.

Refresh the page in the browser and now you'll see the global navigation on every page in the app.

* * *

This concludes the tutorial.

In this tutorial, we went through the process of creating a very simple application where we can list users from a server and edit them individually. As an extra exercise, try to implement user creation and deletion on your own.

If you want to see more examples of Mithril code, check the [examples][18] page. If you have questions, feel free to drop by the [Mithril chat room][19].

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
