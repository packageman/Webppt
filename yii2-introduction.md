title: Yii 2.0 Framework Introduction
speaker: Byron Zhang
url: https://github.com/packageman/Webppt
transition: cards
files: /js/yii2.js,/css/yii2.css

[slide]

# Yii 2.0 Framework Introduction
## 演讲者：Byron Zhang

[slide]

## About Yii
----
- high performance **component-based** PHP framework for rapidly developing modern Web applications.
- The name Yii (pronounced Yee or [ji:]) means **"simple and evolutionary"** in Chinese. It can also be thought
 of as an acronym for **Yes It Is!**

[slide]

## About My Understanding
----
- MVC
- Component-Based (Service Locator, Singleton)
- Special Features (Events & Behaviors & Properties)
- Configurable (DI & Configurations)

[slide]

## Model
----
- attributes() {:&.bounceIn}
- safeAttributes()
- rules()
- fields()
- extraFields()

[note]
### Best Practice
<ul class="middle-text">
<li>may contain attributes to represent business data
<li><span class="text-danger">should contain validation rules to ensure the data validity and integrity</span></li>
<li>may contain methods implementing business logic</li>
<li><span class="text-danger">should NOT directly access request, session, or any other environmental data. These data should be injected by controllers into models</span></li>
<li>should avoid embedding HTML or other presentational code - this is better done in views</li>
<li>avoid having too many scenarios in a single model.</li>
</ul>

[/note]

[slide]

## View - Best Practice
----
- should mainly contain presentational code, such as HTML, and simple PHP code to traverse, format and render data.
- should **NOT** contain code that performs DB queries. Such code should be done in models.
- **should avoid direct access to request data, such as $_GET, $_POST. This belongs to controllers. If request data is - needed, they should be pushed into views by controllers.**
- may read model properties, but should not modify them.

[note]
## Difference between `/` and `//` in view
<ul class="small-text">
    <li>If the view name starts with double slashes //, the corresponding view file path would be @app/views/ViewName. That is, the view is looked for under the application's view path. For example, //site/about will be resolved into <span class="text-info">@app/views/site/about.php</span>.</li>
    <br/>
    <br/>
    <li>If the view name starts with a single slash /, the view file path is formed by prefixing the view name with the view path of the currently active module. If there is no active module, @app/views/ViewName will be used. For example, /user/create will be resolved into <span class="text-info">@app/modules/user/views/user/create.php</span>, if the currently active module is user. If there is no active module, the view file path would be @app/views/user/create.php.</li>
</ul>
[/note]

[slide]

## Controller
----
- behaviors() {:&.rollIn}
- beforeActions()
- actionRules() (implemented by behavior)
- actions()
- afterActions()

[note]
### Best Practice
<ul class="middle-text">
    <li>may access the request data;</li>
    <li>may call methods of models and other service components with request data;</li>
    <li>may use views to compose responses;</li>
    <li><span class="text-danger">should NOT process the request data - this should be done in the model layer;</span></li>
    <li><span class="text-danger">should avoid embedding HTML or other presentational code - this is better done in views.</li>
    <li>use the <span class="text-danger">type name</span> of the resource and use <span class="text-danger">singular</span> form. For example, to serve user information, the controller may be named as UserController.</li>
</ul>
[/note]

[slide]

## Key Concepts
----
- Components {:&.rollIn}
- <span class="text-primary">Properties</span>
- <span class="text-primary">Events</span>
- <span class="text-primary">Behaviors</span>
- Configurations
- Class Autoloading
- <span class="text-danger">Service Locator</span>
- <span class="text-danger">Dependency Injection Container</span>

[slide]

## Components
----
- Built-in components {:&.fadeIn}

    <span class="text-danger">id, basePath,</span> urlManager, log, cache, i18n, response... {:.text-primary}
- Custom components

    mail, qiniu, job, weConnect, tuisongbao... {:.text-primary}

[note]
### The yii\base\Object class enforces the following object lifecycle:
<br/>
1. Pre-initialization within the constructor. You can set default property values here.
2. Object configuration via $config. The configuration may overwrite the default values set within the constructor.
3. Post-initialization within init(). You may override this method to perform sanity checks and normalization of the properties.
4. Object method calls.
[/note]

[slide]

## Properties
----
- The names of such properties are case-insensitive since method names in PHP are case-insensitive.
- **If the name of such a property is the same as a class member variable, the latter will take precedence. **
- The properties can only be defined by **non-static** getters and/or setters. Static methods will not be treated in the same manner.

[slide]

## Events
----
- [Object-Level event handlers](http://www.yiiframework.com/doc-2.0/guide-concept-events.html#event-handlers)
- [Class-Level event handlers](http://www.yiiframework.com/doc-2.0/guide-concept-events.html#class-level-event-handlers)
- [Global events](http://www.yiiframework.com/doc-2.0/guide-concept-events.html#global-events)

[note]
### <a class="text-primary" href="http://www.yiiframework.com/doc-2.0/guide-concept-events.html#event-handlers">An event handler is a PHP callback that gets executed when the event it is attached to is triggered</a>
<br/>
<ul class="small-text">
    <li>a global PHP function specified as a string (without parentheses), e.g., <span class="text-info">'trim'</span></li>
    <li>an object method specified as an array of an object and a method name as a string (without parentheses), e.g., <span class="text-info">[$object, 'methodName']</span></li>
    <li>a static class method specified as an array of a class name and a method name as a string (without parentheses), e.g., <span class="text-info">['ClassName', 'methodName']</span></li>
    <li>an anonymous function, e.g., <span class="text-info">function ($event) { ... }</span></li>
</ul>
[/note]

[slide]

## Behaviors
----
- Attaching Behaviors
    - Overriding **behaviors()** of Component.php (statically)
    - **$component->attachBehavior($name, $behavior)** (dynamically)

- If two behaviors define the same property or method and they are both attached to the same component, <span class="text-warning">the behavior that is attached to the component first will take precedence</span> when the property or method is accessed.


[note]
## <a class="text-primary" href="http://www.yiiframework.com/doc-2.0/guide-concept-behaviors.html#comparison-with-traits">Comparing Behaviors with Traits</a>
Reasons to Use Behaviors

- support inheritance
- can be attached and detached to a component dynamically
- can handle component event
- resolve conflicts automatically

<br/>
Reasons to Use Traits

- much more efficient
- IDEs are more friendly (native language construct)
[/note]

[slide]

## Configurations
----
Configurations are widely used in Yii when **creating new objects or initializing existing objects**.
<br/>
```php
Configuration Format

[
    'class' => 'ClassName',
    'propertyName' => 'propertyValue',
    'on eventName' => $eventHandler,
    'as behaviorName' => $behaviorConfig,
]
```

[slide]

## Class Autoloading
----
1. BaseYii::autoload($className)
2. Yii::$classMap = include(\_\_DIR\_\_ . '/classes.php')
3. Build Class as following rules
    - **Each class must be under a namespace (e.g. foo\bar\MyClass)**
    - **Each class must be saved in an individual file whose path is determined by the following algorithm:**

    ```php
    // $className is a fully qualified class name without the leading backslash
    $classFile = Yii::getAlias('@' . str_replace('\\', '/', $className) . '.php');
    ```

[slide]

## Service Locator
----
- Why we can use component like **\Yii::$app->xxx**? {:&.fadeIn}
- That is what Service Locator does (an object that provide all sorts of services (or components) that an application might need).

[slide]

## Dependency Injection Container
----
- [Container](http://www.yiiframework.com/doc-2.0/guide-concept-di-container.html) 定义并解决依赖关系,使用程序变得<span class="text-primary">可配置,松耦合</span>
    - Constructor injection (type hints or interface)
    - Setter and property injection
    - PHP callable injection
    - Controller action injection (type hints or interface)

- [Service Locator](http://www.yiiframework.com/doc-2.0/guide-concept-service-locator.html) 配置服务的参数信息(Component 单例)
    - **\Yii::$app** 就是一个Service Locator

[note]

## [Service Locator](http://www.digpage.com/service_locator.html)
## configurable and loosely-coupled
[/note]

[slide]

## Handling Requests
----
- Bootstrapping {:&.zoomIn}
- **Routing** and URL Creation
- Requests
- Response
- Handling Errors
- **Logging**

[note]

## 详细介绍 Routing, Logging

[/note]

[slide]

## Yii-request-lifecycle
---
![yii-request-lifecycle](/img/request-lifecycle.png "yii-request-lifecycle")

[note]
### PHP 是一种弱类型语言，在继承上与java有些不同
[/note]

[slide]

## Routing
----
1. Parse the incoming request into a route and the associated query parameters.
2. Create controller action corresponding to the parsed route to handle the request.

[slide]

## Routing - step 1
----
1. Default URL
    - <s>Getting the value of a `GET` query parameter named `r`</s>
2. Pretty URL
    - URL manager examine the registered **URL rules** to find matching one that can resolve the request into a route

[slide]

## Routing - step 2
----
<ul class="small-text">
    <li>1. <span class="text-primary">Set the application as the current module.</span></li>
    <li>2. Check if the **controller map** of the current module contains the current ID. If so, a controller object will be created according to the controller configuration found in the map, and Step 5 will be taken to handle the rest part of the route.</li>
    <li>3. Check if the ID refers to a module listed in the **modules** property of the current module. If so, a module is created according to the configuration found in the module list, and Step 2 will be taken to handle **the next part of the route** under the context of the newly created module.</li>
    <li>4. Treat the ID as a controller ID and create a controller object. Do the next step with the **rest part of the route.**</li>
    <li>5. The controller looks for the current ID in its **action map**. If found, it creates an action according to the configuration found in the map. Otherwise, the controller will attempt to create an inline action which is defined by an action method corresponding to the current action ID.</li>
</ul>
[note]
###### Assume the parsed route is (URL => route)
<span class="text-primary">GET api/helpdesk/issues => helpdesk/issue/index</span>
<br/>
<br/>
1. 创建application module. (id: app-backend)
2. 拆分$route, $id: helpdesk, $route: issue/index, 根据$id创建module helpdesk. (id: helpdesk)
3. 继续拆分$route, $id: issue, $route: index, 根据$id创建issue controller. (id: issue)
4. 根据$route创建action. (id: index)
[/note]

[slide]

## Application

```sh
yii/base/Object
      ↑
yii/base/Component
      ↑
yii/base/ServiceLocator
      ↑
yii/base/Module   7. runAction($route, $params)
                  8. list($controller, $actionID) = $this->createController($route)
      ↑           9. $controller->runAction($actionID, $params);
yii/base/Application   1. __construct($config) => init() => bootstrap()
                       2. run() => 16. $response->send()
      ↑
yii/web/Application  (Entry stript)  3. handleRequest($this->getRequest())
```

[note]
yii/web/Application.php
```php
public function handleRequest($request)
{
    if (empty($this->catchAll)) {
    4. list ($route, $params) = $request->resolve();
    } else {
        ...
    }
    try {
        ...
    7. $result = $this->runAction($route, $params);
        ...
    } catch (InvalidRouteException $e) {
        throw new NotFoundHttpException($e->getMessage(), $e->getCode(), $e);
    }
}
```
[/note]

[slide]

## Request
----
```sh
yii/base/Request
      ↑
yii/web/Request  4. resolve()
```
[note]
yii/web/Request.php
```php
public function resolve()
{
5. $result = Yii::$app->getUrlManager()->parseRequest($this);
    if ($result !== false) {
        list ($route, $params) = $result;
        $_GET = array_merge($_GET, $params);

        return [$route, $_GET];
    } else {
        throw new NotFoundHttpException(Yii::t('yii', 'Page not found.'));
    }
}
```
[/note]

[slide]

## UrlManager
----
<sapn>6. UrlManager 遍历config中配置的*urlRule*进行匹配，其中调用了**yii/web/UrlRule::parseRequest($manager, $request)**方法</span>

[note]
yii/web/UrlManager.php
```php
public function parseRequest($request)
{
    if ($this->enablePrettyUrl) {
        $pathInfo = $request->getPathInfo();
        /* @var $rule UrlRule */
        foreach ($this->rules as $rule) {
         6. if (($result = $rule->parseRequest($this, $request)) !== false) {
                return $result;
            }
        }
    if ($this->enableStrictParsing) {
            return false;
            // 如果没有匹配到对应的urlRule，并且enableStrictParsing设为false,
            // 将会直接将$pathInfo转换成$route。
        }
    ...
}
```
[/note]

[slide]
## Controller
----
<span>8. When executing yii/base/Module::createController(), yii will match route as following order.</span>
    - controllerMap
    - module => module.createController()
    - createControllerById

<span>9. Perform **$controller->runAction($actionID, $params)** after controller is created.</span>


[note]
yii/base/Controller
```php
public function runAction($id, $params = [])
{
10. $action = $this->createAction($id);
    ...
    // call beforeAction on modules
    foreach ($this->getModules() as $module) {
    11. if ($module->beforeAction($action)) {
            array_unshift($modules, $module);
        }
        ...
    }
  12. if ($runAction && $this->beforeAction($action)) {
        // run the action
    13. $result = $action->runWithParams($params);
    14. $result = $this->afterAction($action, $result);

        // call afterAction on modules
        foreach ($modules as $module) {
            /* @var $module Module */
        15. $result = $module->afterAction($action, $result);
        }
    }
}
```
[/note]

[slide]

## Inline Action & Standalone Action
----
<span>10. When performing the method **createAction($id)**, yii will create a standalone action from actionMap(**$this->actions()**) based on *$id* recieved first. If nothing mathched, then yii creates an inline action based on *$id* with prefix **action**.<span>

[note]
yii/base/Controller.php
```php
public function createAction($id)
{
    ...
    $actionMap = $this->actions();
    if (isset($actionMap[$id])) {
        return Yii::createObject($actionMap[$id], [$id, $this]);
    } elseif (preg_match('/^[a-z0-9\\-_]+$/', $id) && strpos($id, '--') === false && trim($id, '-') === $id) {
        $methodName = 'action' . str_replace(' ', '', ucwords(implode(' ', explode('-', $id))));
        if (method_exists($this, $methodName)) {
            $method = new \ReflectionMethod($this, $methodName);
            if ($method->isPublic() && $method->getName() === $methodName) {
                return new InlineAction($id, $this, $methodName);
            }
        }
    }
    ...
}
```
[/note]

[slide]

## Serializer
----
<span>15. Controller performs *afterAction()* will call **serilizer->serialize()**. As a result the return value will be transform to an array.</span>

[note]
yii/rest/Controller.php
```php
public function afterAction($action, $result)
{
    \Yii::error(get_called_class().'::afterAction');
    $result = parent::afterAction($action, $result);
15. return $this->serializeData($result);
}

protected function serializeData($data)
{
    return Yii::createObject($this->serializer)->serialize($data);
}
```
[/note]

[slide]

## Response
----
<span>16. Call **$response->send()** in *yii/base/Application::run()*. In process of sending, *Response* will call **$fromatter->format()** to format serialized array to string with specified format.(e.g.JSON).</span>

[slide]

## Before performing requested actiion
----
#### By default Yii has attached 4 behaviors in yii/rest/Controller.php, each of them has implemented method **beforeAction()**.

<br>

  | contentNegotiator | verbFilter | authenticator | rateLimiter
  :-------------------|:-----------|:--------------|:-----------|:----
介绍 | supports content negotiation | supports HTTP method validation | supports user authentication | supports rate limiting
用途 | (1).Validate "Accept Content Type", by default only `json` and `xml` were permitted.<br/>(2).Versioning, `Module` for major version and `contentNegotiator` for minor versions|It allows to define allowed HTTP request methods for each action. 例如对于actionCreate()方法只能是`POST`请求|(1).Http basic auth<br/>(2).<span class="text-primary">Query parameter</span><br/>(3).OAuth 2|对API的访问频率进行限制

[note]
<br/>
- The method <span class="text-info">beforeAction()</span> will be invoked as event handler when *yii/base/Controller::beforeAction()* triggerring event <span class="text-info">EVENT_BEFORE_ACTION</span>.

<br/>
- About Handling Component Events, refer to http://www.yiiframework.com/doc-2.0/guide-concept-behaviors.html#handling-component-events
[/note]

[slide]

## Logging
----
1. Define the target where you want to output logs (file, db, email, syslog)
2. Use **\Yii::info()** or **\Yii::error()** to print the log.

[note]
### Figure out how an error message was outputted into file. For example you have a syntax error in your code.
1. yii\web\ErrorHandler::handleError()
2. yii\web\ErrorHandler::handleException()
3. yii\web\ErrorHandler::logException()
4. yii\log\Logger::log()
5. yii\log\Logger::flush()
6. yii\log\Dispatcher::dispatch()   <span class="text-danger">(this step will traverse target we have configured in config file)</span>
7. backend\modules\test\components\LogTarget::collect()
8. backend\modules\test\components\LogTarget::export()
9. LogHelper::error()
[/note]

[slide]

## RESTful Web Services
----
1. [RESTful Web Services: A Tutorial](http://www.drdobbs.com/web-development/restful-web-services-a-tutorial/240169069)
2. [Best Practices for Designing a Pragmatic RESTful API](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)
3. https://github.com/inetfuture/technote/blob/master/restful_api.md

[slide]

## Query in multiple models
----
- hasMany() {:&.rollIn}
- hasOne()
- With()

[note]
### You can use `fields()` and/or `$scenario ` to perfect your code when performing operations in multiple models.
[/note]

[slide]

### Use **with()** to improve performance (101 vs 2)
----
```php
// Assume we have 100 customers.
// This approach will access database 101 times.
$customers = Customer::find()->all();   // db.customer.find();
foreach($customers as $customer) {
    $orders = $customer->orders; // db.order.find({customerId: ...});
}

// This approach will access database only 2 times.
// db.customer.find();
// db.order.find(customerId: {$in: [array1, array2...]})
$costomers = Customer::find()->with('orders')->all();
foreach($customers as $customer) {
    $orders = $customer->orders;
}
```

[slide]

### Use **$model->attributes** to do massive assignment instead of assigning value one by one if attributes were safe.
----
<div class="columns-2">
<pre><code class="php">
{
    $user = new User;
    $user->name = $params['name'];
    $user->email = $params['email'];
    $user->phone = $params['phone'];
    $user->avatar = $params['avatar'];
}</code></pre>
<pre><code class="php">
{
    $user->attributes = $params;
    // 内部Yii会读取safeAttributes或
    // 在当前场景下定义为safe的属性
    // 与$params中的对应属性进行匹配，
    // 如果存在且不为null就赋值给$user
}</code></pre>
</div>

[slide]

## Validating Input (Important)
----
- [Guide-validation-input](http://www.yiiframework.com/doc-2.0/guide-input-validation.html)
- [Core Validators](http://www.yiiframework.com/doc-2.0/guide-tutorial-core-validators.html)
- 对于明显存在默认值的属性应该设置**default value**而不是在save()的时候手动赋值。

[slide]

## Data migration
----

[slide]

## API Testing
----
- [Codeception](http://codeception.com/docs/)

[slide]

## Define, Create And Run Resque Jobs
----
- One Time Job
- Recurring Job
- I18N

[slide]

## Performance Consideration
----
- 减少遍历次数 ( Table::getTablesForElastic )
- 利用缓存 （ Controller->cache ）
- 减少网络请求
- 创建数据库索引

[slide]

----
# Thanks
