### 1.**检查相等性**

**严格等于**：

`pm.expect(variable).to.eql(value);`

检查变量的值是否与期望值严格相等（值和类型）。

**大概等于**：

`pm.expect(variable).to.equal(value);`

检查变量的值是否与期望值相等（通常用于数字、字符串）。

### 2.**包含性检查**

**包含元素**：

`pm.expect(array).to.include(member);`

检查数组或字符串中是否包含某个元素或子串。

**对象包含属性**：

`pm.expect(object).to.have.property('key');`

检查对象是否包含指定的属性。

### 3.**比较大小**

**在两者之间**：

`pm.expect(number).to.be.within(start, end);`

检查数值是否在指定的范围内。

**大于**：

`pm.expect(number).to.be.above(value);`

检查数值是否大于某个值。

**小于**：

`pm.expect(number).to.be.below(value);`

检查数值是否小于某个值。

### 4.**类型检查**

**类型验证**：

`pm.expect(variable).to.be.a('type');`

检查变量的类型是否为指定的类型（如 ‘string’, ‘number’, ‘array’）。

### 5.**存在性检查**

**存在**：

`pm.expect(variable).to.exist;`

检查变量是否存在（不是 null 或 undefined）。

**为空**：

`pm.expect(array).to.be.empty;`

检查数组、字符串或对象是否为空。

### 6.**真值检查**

**为真**：

`pm.expect(boolean).to.be.true;`

检查变量的布尔值是否为真。

**为假**：

`pm.expect(boolean).to.be.false;`

检查变量的布尔值是否为假。

### 7.**高级比较和逻辑**



1. **长度验证**

**长度**：

`pm.expect(array).to.have.lengthOf(number);`

检查数组、字符串或任何具有 .length 属性的对象的长度。

2. **对象深度比较**

**深度等于**：

`pm.expect(object).to.deep.equal({ key: value });`

进行深度比较，验证对象的结构和内容是否与期望的对象完全相同。

3. **多项断言链**

**链式使用**：

`pm.expect(number).to.be.above(10).and.below(20);`

使用 .and 连接多个断言，对同一个目标进行多重验证。

4. **任一条件满足**

**至少满足一个条件**：

`pm.expect(value).to.satisfy(function(num) { return num > 18 || num < 10; });`

使用 .satisfy 和一个函数来检查数据是否满足自定义的任一条件。



### 8.**字符串和数组特有断言**



1. **字符串匹配**

**匹配正则表达式**：

`pm.expect(string).to.match(/regex/);`

检查字符串是否符合指定的正则表达式。

2. **数组包含**

**包含子集**：

`pm.expect(array).to.include.members([partialArray]);`

检查数组是否包含期望的所有元素，无论顺序如何。



### **响应特有断言**



1. **响应时间**

**响应时间检查**：

`pm.expect(pm.response.responseTime).to.be.below(500);`

检查响应时间是否在预期的毫秒数以下。

2. **状态码**

**状态码验证**：

`pm.expect(pm.response.code).to.eql(200);`

验证返回的 HTTP 状态码是否为期望值。

3. **头部验证**

**头部内容**：

`pm.expect(pm.response.headers).to.have.property('Content-Type', 'application/json');`

检查响应头是否包含指定的属性和值。