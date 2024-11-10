## Open Weather—Postman测试Day2

### **测试1: 检查城市名是否为“London”**

`pm.test("City name is London", function () {`
    `var jsonData = pm.response.json();`
    `pm.expect(jsonData.name).to.eql("London");`
`});`

### **测试2: 检查返回的状态码是否为200**

`pm.test("Status code is 200", function () {`
    `pm.expect(pm.response.code).to.eql(200);`
`});`

### **测试3: 检查是否正确返回温度和湿度信息**

`pm.test("Temperature and humidity are present", function () {`
    `var jsonData = pm.response.json();`
    `pm.expect(jsonData.main).to.have.property("temp");`
    `pm.expect(jsonData.main.temp).to.be.a("number");`
    `pm.expect(jsonData.main).to.have.property("humidity");`
    `pm.expect(jsonData.main.humidity).to.be.a("number");`
`});`

### **测试4: 检查风速和风向**

`pm.`test("Wind speed and direction are correct", function () {`
    `var jsonData = pm.response.json();`
    `pm.expect(jsonData.wind.speed).to.eql(5.75);`
    pm.expect(jsonData.wind.deg).to.eql(240);`
});

### **测试5: 检查云量**

`pm.test("Cloudiness is 100%", function () {`
    `var jsonData = pm.response.json();`
    `pm.expect(jsonData.clouds.all).to.eql(100);`
`});`