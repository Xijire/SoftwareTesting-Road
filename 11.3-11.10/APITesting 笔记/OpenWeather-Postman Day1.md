## **任务 1：基础天气数据请求**



### **目标**：发送一个基本的 GET 请求获取某个城市的天气数据，验证以下内容：

​	•	响应状态码是否为 200

​	•	响应时间是否小于 500ms

​	•	响应中是否包含 weather 和 main 字段

​	•	main.temp 是否为数值类型

### **测试脚本**

#### •响应状态码是否为 200

我写了这个

`pm.test('Status is 200', function () {`
    `pm.expect(pm.response.status).to.eql(200);`
`});`

但是出现了AssertionError: expected 'OK' to equal 200，这个错误说明 pm.response.status 返回了字符串 'OK'，而不是数字 200。在 Postman 中，有时候 status 返回的是状态文本（如 'OK'），而不是数值型状态码。要解决这个问题，可以直接使用 pm.response.code 来检查状态码。

**1. 使用 pm.response.code 检查状态码**

`pm.test('Status is 200', function () {`
    `pm.expect(pm.response.code).to.equal(200);`
`});`

**2. 使用 .statusText 断言状态文本**

`pm.test("Status code is 200", function () {`
    `pm.response.to.have.status(200);`
`});`

**3. 使用简化的 Postman 状态断言**

`pm.test("Status code is 200", function () {`
    `pm.response.to.have.status(200);`
`});`

**4. 使用.to.equal替代.eql**

`pm.test('Status is 200', function () {`
    `pm.expect(pm.response.status).to.equal(200);`
`});`

##### PS:

`pm.test('Status is 200', function () {`

​	`pm.expect(pm.response.status).to.eql(200);`

`});` 

和

`pm.test("Status code is 200", function () {`

  `pm.response.to.have.status(200);`

`});`

有什么区别?

上面的用于深度比较。它不只是简单地检查类型和值的匹配，还会在对象或数组等复杂结构中进行内容匹配。第二个写法专门用于检查状态码。

#### •响应时间是否小于 500ms

`pm.test('Response time is less than 500ms', function (){`

​    `pm.expect(pm.response.responseTime).to.be.below(500);`

`});`

#### •响应中是否包含 weather 和 main 字段

我写的

`pm.test('Response contains weather and main', function (){`

​    `pm.expect(pm.response).to.have.property('weather')`

`});`

出现Response contains weather and main | AssertionError: expected PostmanResponse{ …(11) } to have property 'weather'

当响应体是 JSON 格式时，可以使用 pm.response.json() 方法将其转换成一个 JavaScript 对象。这样做的好处是，一旦将 JSON 响应转换成 JavaScript 对象，就可以使用 JavaScript 语法轻松地访问和操作数据。

所以正确写法是

`pm.test("Response contains 'weather' and 'main'", function () {`

  `var jsonData = pm.response.json(); // 获取 JSON 格式的响应体`

  `pm.expect(jsonData).to.have.property('weather');`

  `pm.expect(jsonData).to.have.property('main');`

`});`

#### •main.temp 是否为数值类型

`pm.test('Temperature field is a number', function (){`

​    `var jsonData = pm.response.json();`

​    `pm.expect(jsonData.main.temp).to.be.a('number')`

`});`

​	•	.main：在此上下文中，main 是响应 JSON 对象的一个属性。它本身也是一个对象，包含了天气相关的一些主要信息，如温度、湿度等。

​	•	.temp：这是 main 对象中的一个属性，表示当前的温度。在 OpenWeatherMap API 的响应中，这通常是一个浮点数，表示摄氏度或华氏度（根据请求的单位参数而定）的温度值。

​	•	.to.be.a("number")：这是一个链式调用，用于指定您期望的数据类型。在这个例子中，.to.be.a("number") 检查 jsonData.main.temp 是否为数字类型。这是确保 API 返回了正确格式数据的重要测试，特别是在处理需要计算或显示的数值时。



main其实就是API文档中的一个对象，也就是说我要测试前需要知道有没有main这个对象。

## **任务 2：不同城市的天气数据批量请求**



### **目标**：使用不同的城市名称进行批量请求，确保：

​	•	响应时间合理（小于 500ms）

​	•	每个城市的 main.temp 字段存在且在合理温度范围内

​	•	返回的城市名称和请求的城市一致

### 测试脚本



**使用环境变量**（修改完一定要保存，不然Run的时候会出现error）

1. **创建环境变量**：在 Postman 的环境配置中，您可以创建一个新的环境，例如命名为 “WeatherAPI”。

2. **添加变量**：在该环境中添加一个变量，例如命名为 city，您可以先赋予它一个默认值，如 “London”。

	3.	**设置请求 URL**：修改您的 GET 请求的 URL，将 q 参数的值设置为引用您的环境变量。例如：`https://api.openweathermap.org/data/2.5/weather?q={{city}}&appid=YOUR_API_KEY`
	3.	**选择环境并发送请求**：在发送请求前，请确保选择了正确的环境（在 Postman 界面右上角）。更改环境变量 city 的值来测试不同的城市。

**使用数据文件进行批量请求**

 1. **准备数据文件**：创建一个 CSV 或 JSON 文件，列出您想测试的城市。例如，CSV 文件的内容可能如下：

    city
    London
    Paris
    New York
    Tokyo

在文本编辑中写text，最后保存为纯文本格式，确保文件名以 .csv 结尾，比如 cities.csv

**Collection Runner**

![image-20241109225901749](/Users/max/Library/Application Support/typora-user-images/image-20241109225901749.png)

![image-20241109230138561](/Users/max/Library/Application Support/typora-user-images/image-20241109230138561.png)

![image-20241109230218458](/Users/max/Library/Application Support/typora-user-images/image-20241109230218458.png)

#### •每个城市的 main.temp 字段存在且在合理温度范围内

`pm.test("Temperature field exists and is in reasonable range", function () {`

​    `var jsonData = pm.response.json();`

​    `pm.expect(jsonData.main).to.have.property("temp");`

​    `pm.expect(jsonData.main.temp).to.be.within(-50, 300);  // 根据实际气温范围，先看一下body中的气温，不然会报错`

`});`



#### •返回的城市名称和请求的城市一致

`pm.test("City name matches requested city", function () {`

​    `var jsonData = pm.response.json();`

​    `var requestedCity = pm.variables.get("city");  // 使用 CSV 文件的城市名称，目前的文件就是city，这一步会查看4个城市`

​    `pm.expect(jsonData.name).to.eql(requestedCity);`

`});`

## **任务 3：扩展数据请求（单位转换）**

### **目标**：使用 units 参数测试温度单位（metric 和 imperial），确保：

​	•	温度字段符合设定的单位：华氏时合理范围在 30°F 至 320°F

​	•	请求中指定的单位是否被正确设置并返回

#### 测试脚本

`pm.test("Temperature is in Fahrenheit for imperial units", function () {`

​    `var jsonData = pm.response.json();`

​    `pm.expect(jsonData.main.temp).to.be.a("number");`

​    `pm.expect(jsonData.main.temp).to.be.within(30, 320);`

​    `pm.expect(pm.request.url.query.get("units")).to.eql("imperial");`

`});`

**解析 pm.request.url.query.get("units")**



​	1.	pm.request：

​	•	pm.request 是 Postman 提供的一个对象，它包含了当前发送的 HTTP 请求的所有信息。

​	•	这包括请求的 URL、方法（GET、POST 等）、头部信息（Headers）、请求体（Body）等。

​	2.	url：

​	•	在 pm.request 对象中，url 属性持有与请求相关的 URL 信息。

​	3.	query：

​	•	url.query 是 url 对象的一个属性，它专门用来处理 URL 的查询字符串部分。

​	•	查询字符串指的是 URL 中 ? 后面的部分，通常包含一系列的键值对（例如 `?units=metric&appid=your_api_key`）。

​	4.	.get("units")：

​	•	.get("units") 是一个方法，用于从查询字符串中获取键名为 “units” 的值。

​	•	如果 URL 是这样的：`https://api.example.com/data?units=imperial&appid=your_api_key`，那么 pm.request.url.query.get("units") 将返回 “imperial”。



## **任务 4：查询特定地理位置的天气数据**



### **目标**：通过经纬度查询天气数据，验证：

​	•	返回的城市名称与预期一致

​	•	返回的经纬度与请求的值匹配

### 测试脚本

#### •返回的城市名称与预期一致

`pm.test("Correct city name in response", function () {`

​    `var jsonData = pm.response.json();`

​    `var requestedCity = pm.variables.get("city");  // 从变量中获取城市名称`

​    `pm.expect(jsonData.name).to.eql(requestedCity);  // 检查响应中的城市名称是否与请求的相符`

`});`

#### •返回的经纬度与请求的值匹配

`pm.test("Latitude and longitude match request", function () {`

​    `var jsonData = pm.response.json();`

​    `var requestedLat = parseFloat(pm.variables.get("lat"));  // 从变量中获取纬度`

​    `var requestedLon = parseFloat(pm.variables.get("lon"));  // 从变量中获取经度`

​    `pm.expect(jsonData.coord.lat).to.eql(requestedLat);`

​    `pm.expect(jsonData.coord.lon).to.eql(requestedLon);`

`});`

但是返回了`Latitude and longitude match request | AssertionError: expected 51.5085 to deeply equal 51.5074`

收到这样的错误提示 AssertionError: expected 51.5085 to deeply equal 51.5074 表示测试期望的纬度值和实际从 API 响应中得到的值之间存在微小的差异。这在地理位置数据的测试中很常见，特别是当使用真实世界数据时，由于多种原因，这些数据可能不会完全匹配。



**原因分析**



​	1.	**数据精度问题**：API 响应中的地理位置数据可能会有精度上的微调，这可以由数据源的更新、API内部的处理逻辑（如四舍五入）或者地图数据的微小更改引起。

​	2.	**测试设计**：在设计测试时，对于浮点数类型的数据（如经纬度），常常需要考虑到数据的精度和浮动范围，而不是期望完全相等（完全匹配可能是不切实际的）。



**解决方法**



为了更实际地处理这种类型的测试，您可以考虑使用 Postman 的 pm.expect().to.be.closeTo() 方法，该方法允许您为比较设定一个容差范围，这对于经纬度这类可能会有微小浮动的数据尤其有用。



**修改测试脚本示例**



`pm.test("Latitude matches request within a tolerance", function () {`

  `var jsonData = pm.response.json();`

  `pm.expect(jsonData.coord.lat).to.be.closeTo(51.5074, 0.01); // 允许0.01的误差范围`

`});`



`pm.test("Longitude matches request within a tolerance", function () {`

  `var jsonData = pm.response.json();`

  `pm.expect(jsonData.coord.lon).to.be.closeTo(-0.1278, 0.01); // 允许0.01的误差范围`

`});`



这里的 0.01 是误差容忍度，根据实际需要您可以调整这个值。这种方式能够让您的测试更加健壮，减少因微小的数据变动导致的测试失败。



**核查和调整**



​	•	**核查数据**：确保测试数据的正确性和最新性。检查数据来源是否有变更或数据是否有更新。

​	•	**文档和规格**：查阅API文档，了解其关于经纬度数据处理的具体信息，包括数据精度和可能的数据范围。

​	•	**实时数据对比**：如果可能，与实时地图服务（如 Google Maps API）的数据做对比，看是否存在系统性偏差。



使用 to.be.closeTo 方法而非 to.eql 来进行测试，可以在确保测试的准确性的同时，避免因数据的微小变动而导致的不必要的测试失败。这在处理实际应用中的地理数据时尤为重要。

## **任务 5：预请求脚本和动态值**



### **目标**：使用预请求脚本生成随机的城市名称，并确保：

​	•	请求发送的城市名称和响应中的一致

​	•	响应状态码为 200



#### 测试脚本

1. 设置环境变量，添加变量random_city,不用写任何数据。在请求部分添加city=random_city

2. Pre-request部分

`const cities = ["London", "Paris", "New York", "Tokyo"];`

`const randomCity = cities[Math.floor(Math.random() * cities.length)];`

`pm.environment.set("random_city", randomCity);`

`console.log("Selected city: ", pm.environment.get("random_city"));`

3. Pre-response部分

`pm.test("City name matches randomly selected city", function () {`

​    `var jsonData = pm.response.json();`

​    `var expectedCity = pm.environment.get("random_city");`

​    `pm.expect(jsonData.name).to.eql(expectedCity);`

`});`

`pm.test("Status code is 200", function () {`

​    `pm.response.to.have.status(200);`

`});`

最终结果显示不稳定，有时通过有时不通过，可能是动态数据问题。

## **任务 6：天气预报数据测试**



### **目标**：获取城市天气预报（例如未来 5 天，每 3 小时），并验证：

​	•	list 数组是否包含多项（大于 5）

​	•	每项数据中 temp 和 humidity 字段是否存在

#### 测试脚本

首先需要在OpenWeather网站找到未来5天3个小时的API，创建一个新的Get 请求

<img src="/Users/max/Library/Application Support/typora-user-images/image-20241110171824306.png" alt="image-20241110171824306" style="zoom:50%;" />

然后在变量处选择一个城市，用q=Paris,或者用lan,lon显示地理坐标也可以。

然后用

`pm.test("Forecast list has multiple entries", function () {`

​    `var jsonData = pm.response.json();`

​    `pm.expect(jsonData.list).to.be.an("array").that.is.not.empty;`

​    `pm.expect(jsonData.list.length).to.be.above(5); // 检查是否包含多个时间段的数据`

`});`

`pm.test("Each forecast entry has temperature and humidity", function () {`

​    `var jsonData = pm.response.json();`

​    `jsonData.list.forEach(function (forecast) {`

​        `pm.expect(forecast.main).to.have.property("temp");`

​        `pm.expect(forecast.main).to.have.property("humidity");`

​    `});`

`});`

## **任务 7：响应数据的条件测试**



### **目标**：当天气描述为 “clear sky” 时，测试数据内容并验证：

​	•	响应中 weather.description 字段包含“clear sky”

​	•	如果是晴天，温度应该在 10-35°C 的范围内

#### 测试脚本

`// 测试天气描述是否包含 "clear sky"`
`pm.test("Weather description contains 'clear sky'", function () {`
    `var jsonData = pm.response.json();`
    `var weatherDescription = jsonData.weather[0].description;`
    `pm.expect(weatherDescription).to.include("clear sky");`
`});`

`// 如果是晴天，检查温度是否在 10-35°C 范围内`
`pm.test("Temperature is within 10-35°C when clear sky", function () {`
    `var jsonData = pm.response.json();`
    `var temperature = jsonData.main.temp;`
    `// 转换开尔文温度到摄氏度，假设API返回的温度是以开尔文为单位`
    `var tempCelsius = temperature - 273.15;`
    `if (jsonData.weather[0].description === "clear sky") {`
        `pm.expect(tempCelsius).to.be.within(10, 35);`
    `}`
`});`

出现了错误**Weather description contains 'clear sky' | TypeError: Cannot read properties of undefined (reading '0')**

您遇到的错误信息 **"TypeError: Cannot read properties of undefined (reading '0')"** 指出了尝试访问一个不存在的数组元素。这通常意味着在尝试访问 **jsonData.weather[0].description** 时，weather 数组为空或未定义。



要解决这个问题，您需要确保在尝试访问 weather 数组及其属性之前，该数组确实存在且至少包含一个元素。以下是如何修改测试脚本来避免这种错误的示例：

`pm.test("Weather description contains 'clear sky'", function () {`
    `var jsonData = pm.response.json();`
    `// 首先检查 'weather' 是否存在并且是一个数组`
    `pm.expect(jsonData.weather, "Weather data is not available").to.be.an('array').that.is.not.empty;`
    `// 由于已确认数组不为空，可以安全地访问第一个元素`
    `var weatherDescription = jsonData.weather[0].description;`
    `// 然后检查描述是否包含 'clear sky'`
    `pm.expect(weatherDescription, "Weather description does not match").to.include("clear sky");`
`});`



又遇到了**Weather description contains 'clear sky' | AssertionError: Weather data is not available: expected undefined to be an array**。遇到错误 "AssertionError: Weather data is not available: expected undefined to be an array" 表示您尝试验证的 weather 字段在 API 响应中不存在。这个问题可能是因为 API 的响应结构与您期望的不匹配，或者您发送的请求不正确。

​	1.	**检查 API 响应**：

​	•	在 Postman 中发送请求后，查看“Body”部分的实际响应数据。检查是否真的存在 weather 字段，以及其结构是否如您所期望。

​	•	如果在 Postman 的响应视图中看不到 weather 字段，可能是因为请求参数不正确（例如城市名拼写错误、API密钥问题等），或者您请求的端点不是预期的那个。

​	2.	**验证请求的端点和参数**：

​	•	确保您的请求 URL 是正确的，特别是检查端点是否为获取天气数据的端点。比如 OpenWeatherMap 提供多个不同功能的端点。

​	•	核对请求的参数，如 q（城市名）、appid（API 密钥）以及任何其他可能影响输出的参数。

​	3.	**查阅 API 文档**：

​	•	访问 API 提供者的文档，了解您正在使用的特定 API 端点的详细信息。确认返回的数据格式和包含的字段。

​	•	文档中可能有字段的条件说明，比如某些字段只在特定条件下才出现。

​	4.	**调整测试逻辑**：

​	•	如果发现 weather 字段确实可能不总是存在，您需要修改测试脚本以适应这种情况，例如：pm.test("Weather description contains 'clear



**最终版本为**

`// 遍历每个预报条目，检查天气描述并验证温度`
`pm.test("Clear sky description and temperature check", function () {`
    `var jsonData = pm.response.json();`
    `jsonData.list.forEach(function (entry) {`
        `entry.weather.forEach(function (weather) {`
            `if (weather.description === "clear sky") {`
                `console.log("Clear sky found, checking temperature...");`
                `// 温度转换为摄氏度，温度单位是开尔文`
                `let tempCelsius = entry.main.temp - 273.15;`
                `// 检查温度是否在10到35°C之间`
                `pm.expect(tempCelsius, Temp is out of expected range: ${tempCelsius}C).to.be.within(10, 35);`
            `}`
        `});`
    `});`
`});`

但出现了**Clear sky description and temperature check | AssertionError: Temp is out of expected range: 9.189999999999998C: expected 9.189999999999998 to be within 10..35。**该错误表明，在某个天气描述为 “clear sky” 的时间点，检测到的温度（转换为摄氏度后约为 9.19°C）未能满足设定的温度范围 10°C 到 35°C。这说明实际天气数据中的温度有时可能低于或高于预期的范围，这种情况在现实世界中是有可能发生的，特别是在气候多变的地区。修改为

`pm.test("Clear sky description and temperature check", function () {`
    `var jsonData = pm.response.json();`
    `jsonData.list.forEach(function (entry) {`
        `entry.weather.forEach(function (weather) {`
            `if (weather.description === "clear sky") {`
                `console.log(Checking temperature for clear sky on ${entry.dt_txt});`
                `let tempCelsius = entry.main.temp - 273.15;`
                `pm.expect(tempCelsius, Date: ${entry.dt_txt}, Temp: ${tempCelsius}C).to.be.within(10, 35);`
            `}`
        `});`
    `});`
`});`

结果还是失败。在处理实时数据和环境数据时（如天气预报），测试脚本失败并不总是指示代码或逻辑错误。这种情况下，失败可能只是反映了现实世界数据的变化和不可预测性。这就是为什么在设计这类测试时，应考虑以下几点：

​	1.	**数据变异性**：天气数据尤其会受到多种因素的影响，如地理位置、季节变化和突发气候事件，这些都可能导致数据在不同时间点有很大的不同。

​	2.	**测试设计**：设计测试时，应当设定灵活、实际的预期值，并在可能的情况下留有边际。例如，考虑到气温在清晨和夜间可能会降得比较低，即使是在晴朗的日子。

​	3.	**敏感性和特异性**：

​	•	**敏感性**（Sensitivity）：测试应能捕捉到所有相关和重要的情况（例如，“clear sky”时的异常高温或低温）。

​	•	**特异性**（Specificity）：测试应排除那些虽然技术上符合条件但实际上不相关的情况（如季节性低温）。

​	4.	**错误处理**：在测试中实现适当的错误处理和记录机制，以便在测试失败时提供足够的信息来判断是否是由于一个真正的问题还是由于数据的正常波动。

​	5.	**测试结果的解释**：测试结果应该是持续监控和评估的一部分，而不是绝对的成功或失败的指示。失败的测试可以是进一步调查的触发器，而不是最终判断。

**实施建议**

在实施天气数据相关的测试时，可能需要定期回顾和调整测试逻辑和阈值，确保它们与当前的天气模式和预测技术保持同步。此外，将测试结果与历史数据和趋势比较，可能有助于识别数据波动的正常范围。