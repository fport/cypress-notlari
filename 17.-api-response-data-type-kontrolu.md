# 🦆 Api Response Data Type Kontrolu

Api isteklerimizden dönen response datamızın tiplerini oluşturdup kontrol ettiğimiz bir yöntemi görelim.



**chai** ve **chai-json-schema-ajv** yardımcı kütüphanelerimizi yükleyelim.

```bash
yarn add -D chai 
yarn add -D chai-json-schema-ajv 

# ya da 
yarn add -D chai chai-json-schema-ajv 
```

Gerekli kütüphanelerimizi kurmuş olduk. &#x20;

Şimdi sıra **cypress/support/e2e.js** dosyası altında gerekli importları yapalım.

```javascript

import "./commands";

import chai from "chai";
import chaiJsonSchemaAjv from "chai-json-schema-ajv";

chai.use(chaiJsonSchemaAjv);
```

****

**cypress/e2e/** dizisinde **schema-control-example.cy.js** test dosyamızı oluşturalım. İçeriğini aşağıdaki gibi dolduralım.



```javascript
/*
    @description
    expect özelliğini chai kütüphanesinden alıyoruz,
    mevcut chai-json-schema-ajv kütüphanesini chai kütüphanesine ekliyoruz,
    mevcut expect özelliğini chai ile eziyoruz.

    @docs
    // https://www.npmjs.com/package/chai-json-schema-ajv
*/

const { expect } = require("chai").use(require("chai-json-schema"));

describe("schema-control-example", () => {
	it("JSON schema validator", () => {
		const badApple = {
			colors: ["brown"],
			taste: 0,
			worms: 2,
		};

		const goodApple = {
			skin: "thin",
			colors: ["red", "green", "yellow"],
			taste: 10,
		};

		const fruitSchema = {
			title: "fresh fruit schema v1",
			type: "object",
			required: ["skin", "colors", "taste"],
			properties: {
				colors: {
					type: ["array", "null"], // null değerleri de kabul eder
					minItems: 1,
					uniqueItems: true,
					items: {
						type: "string",
					},
				},
				skin: {
					type: "string",
				},
				taste: {
					type: "number",
					minimum: 5,
				},
			},
		};

		expect(badApple).to.not.be.jsonSchema(fruitSchema);
		expect(goodApple).to.be.jsonSchema(fruitSchema);
	});
});

```
