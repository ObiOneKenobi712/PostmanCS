# PostmanCS
PostmanCS
// ===========================================

// 1. PODSTAWOWE SPRAWDZENIE STATUS CODE  
pm.test("Status code is 200", function () {  
    pm.response.to.have.status(200);
});

// 2. CZAS ODPOWIEDZI  
pm.test("Response time is less than 500ms", function () {  
    pm.expect(pm.response.responseTime).to.be.below(500);
});

// 3. SPRAWDZENIE HEADER  
pm.test("Content-Type is application/json", function () {  
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});

// 4. SPRAWDZENIE, ŻE RESPONSE NIE JEST PUSTY  
pm.test("Response body is not empty", function () {  
    pm.expect(pm.response.text()).to.not.be.empty;
});

// 5. WALIDACJA JSON SCHEMA  
// Schema sprawdza:  
// - Czy response jest obiektem (type: "object")  
// - Czy wszystkie wymagane pola są obecne (required: [...])  
// - Czy każde pole ma poprawny typ danych (properties: {...})  
// - Czy NIE MA dodatkowych pól (additionalProperties: false)  
// To jest SZYBKI test który w jednym kroku weryfikuje całą strukturę odpowiedzi
  
  const schema = {  
  type: "object",  // Response MUSI być obiektem JSON  
  required: ["zupa", "zupaid", "danie", "danieid", "nameNo", "number"],  // Te pola MUSZĄ istnieć  
  additionalProperties: false,  // BLOKUJE dodatkowe, nieoczekiwane pola  
  properties: {  
  zupa: {  
  type: "string"  // zupa MUSI być stringiem  
  },  
  zupaid: {  
  type: "integer"  // zupaid MUSI być liczbą całkowitą  
  },  
  danie: {  
  type: "string"  // danie MUSI być stringiem  
  },  
  danieid: {  
  type: "integer"  // danieid MUSI być liczbą całkowitą  
  },  
  nameNo: {  
  type: "integer"  // nameNo MUSI być liczbą całkowitą  
  },  
  number: {  
  type: "integer"  // number MUSI być liczbą całkowitą  
  }  
  }  
  };
  
 pm.test("Schema is valid", function () {  
 pm.response.to.have.jsonSchema(schema);  
 });

// 6. PARSOWANIE RESPONSE BODY  
// Ten test sprawdza czy response da się sparsować jako JSON  
// Jeśli server zwróci 
// ✅ { "zupa": "Rosół" } - OK, to jest poprawny JSON  
// ❌ { zupa: "Rosół" - FAIL, brak zamykającego nawiasu  
// ❌ <html>Error</html> - FAIL, to HTML nie JSON  
// ❌ Internal Server Error - FAIL, to plain text  
// ❌ undefined - FAIL, pusty response  
//   
// Ten test musi przejść PRZED wszystkimi innymi testami które używają jsonData!
  
  let jsonData;  
 try {  
 jsonData = pm.response.json();  // Próba parsowania response jako JSON  
 } catch (e) {  
 pm.test("Response is valid JSON", function () {  
 throw new Error("Response is not valid JSON: " + e.message);  
 });  
 }
  
  // 7. SPRAWDZENIE KAŻDEGO POLA - ISTNIENIE I TYP
  
  // Sprawdzenie pola 'zupa'  
  pm.test("Field 'zupa' exists and is a string", function () {  
  pm.expect(jsonData).to.have.property('zupa');  
  pm.expect(jsonData.zupa).to.be.a('string');  
  });


// Schema sprawdza tylko TYP, nie WARTOŚĆ  
// Ten test sprawdza czy nazwa ma sens   
pm.test("Field 'zupa' is not empty", function () {  
pm.expect(jsonData.zupa).to.not.be.empty;  
});
  
  pm.test("Field 'zupa' has reasonable length", function () {  
  pm.expect(jsonData.zupa.length).to.be.at.least(2);  
  pm.expect(jsonData.zupa.length).to.be.at.most(100);  
  });
  
  // Sprawdzenie pola 'zupaid'  
  pm.test("Field 'zupaid' exists and is an integer", function () {  
  pm.expect(jsonData).to.have.property('zupaid');  
  pm.expect(jsonData.zupaid).to.be.a('number');  
  pm.expect(Number.isInteger(jsonData.zupaid)).to.be.true;  
  });
  
  pm.test("Field 'zupaid' is a positive integer", function () {  
  pm.expect(jsonData.zupaid).to.be.above(0);  
  });
  
  // Sprawdzenie pola 'danie'  
  pm.test("Field 'danie' exists and is a string", function () {  
  pm.expect(jsonData).to.have.property('danie');  
  pm.expect(jsonData.danie).to.be.a('string');  
  });
  
  pm.test("Field 'danie' is not empty", function () {  
  pm.expect(jsonData.danie).to.not.be.empty;  
  });
  
  pm.test("Field 'danie' has reasonable length", function () {  
  pm.expect(jsonData.danie.length).to.be.at.least(2);  
  pm.expect(jsonData.danie.length).to.be.at.most(100);  
  });
  
  // Sprawdzenie pola 'danieid'  
  pm.test("Field 'danieid' exists and is an integer", function () {  
  pm.expect(jsonData).to.have.property('danieid');  
  pm.expect(jsonData.danieid).to.be.a('number');  
  pm.expect(Number.isInteger(jsonData.danieid)).to.be.true;  
  });
  
  pm.test("Field 'danieid' is a positive integer", function () {  
  pm.expect(jsonData.danieid).to.be.above(0);  
  });
  
  // Sprawdzenie pola 'nameNo'  
  pm.test("Field 'nameNo' exists and is an integer", function () {  
  pm.expect(jsonData).to.have.property('nameNo');  
  pm.expect(jsonData.nameNo).to.be.a('number');  
  pm.expect(Number.isInteger(jsonData.nameNo)).to.be.true;  
  });
  
  pm.test("Field 'nameNo' is a positive integer", function () {  
  pm.expect(jsonData.nameNo).to.be.above(0);  
  });
  
  // Sprawdzenie pola 'number'  
  pm.test("Field 'number' exists and is an integer", function () {  
  pm.expect(jsonData).to.have.property('number');  
  pm.expect(jsonData.number).to.be.a('number');  
  pm.expect(Number.isInteger(jsonData.number)).to.be.true;  
  });
  
  pm.test("Field 'number' is a positive integer", function () {  
  pm.expect(jsonData.number).to.be.above(0);  
  });
  
  // 8. SPRAWDZENIE RELACJI MIĘDZY POLAMI (Business Logic)  
  pm.test("zupaid and danieid are different", function () {  
  pm.expect(jsonData.zupaid).to.not.equal(jsonData.danieid);  
  });
  
  // 9. SPRAWDZENIE, ŻE NIE MA DODATKOWYCH NIEOCZEKIWANYCH PÓL  
  // UWAGA: Ten test jest REDUNDANTNY jeśli masz "additionalProperties: false" w schema  
  // Możesz go usunąć jeśli schema ma tę opcję włączoną  
  // Zostawiam go jako backup dla starszych wersji JSON Schema validator  
  pm.test("Response has only expected fields", function () {  
  const expectedFields = ["zupa", "zupaid", "danie", "danieid", "nameNo", "number"];  
  const actualFields = Object.keys(jsonData);
    
    pm.expect(actualFields.sort()).to.eql(expectedFields.sort());  
  });
  
  // 10. WALIDACJA STRINGÓW - sprawdzenie czy nie zawierają special characters  
  pm.test("String fields don't contain SQL injection patterns", function () {  
  const sqlPatterns = /(\bSELECT\b|\bUNION\b|\bDROP\b|\bINSERT\b|\bDELETE\b)/i;  
  pm.expect(jsonData.zupa).to.not.match(sqlPatterns);  
  pm.expect(jsonData.danie).to.not.match(sqlPatterns);  
  });
  
  pm.test("String fields don't contain XSS patterns", function () {  
  const xssPatterns = /(<script|javascript:|onerror=|onload=)/i;  
  pm.expect(jsonData.zupa).to.not.match(xssPatterns);  
  pm.expect(jsonData.danie).to.not.match(xssPatterns);  
  });
  
  // 11. ZAPISANIE DO ZMIENNYCH ŚRODOWISKOWYCH (jeśli potrzebujesz w kolejnych requestach)  
  pm.test("Save response data to environment", function () {  
  pm.environment.set("last_zupaid", jsonData.zupaid);  
  pm.environment.set("last_danieid", jsonData.danieid);  
  pm.environment.set("last_nameNo", jsonData.nameNo);  
  });
  
  // 12. SPRAWDZENIE UNIQUE CONSTRAINTS (jeśli masz kolekcję danych)  
  // Przykład: jeśli pobieras listę obiektów  
  // pm.test("All IDs are unique", function () {  
  //     const ids = jsonData.map(item => item.zupaid);  
  //     const uniqueIds = [...new Set(ids)];  
  //     pm.expect(ids.length).to.equal(uniqueIds.length);  
  // });
  
  // 13. CONSOLE LOG DLA DEBUGOWANIA  
  console.log("Response Data:", JSON.stringify(jsonData, null, 2));  
  console.log("Response Time:", pm.response.responseTime, "ms");
  
  // 14. DODATKOWE SPRAWDZENIE - ENCODING  
  pm.test("Response encoding is correct (UTF-8)", function () {  
  // Sprawdzenie czy polskie znaki są poprawnie zakodowane  
  const polishChars = /[ąćęłńóśźżĄĆĘŁŃÓŚŹŻ]/;  
  if (polishChars.test(jsonData.zupa) || polishChars.test(jsonData.danie)) {  
  pm.expect(pm.response.headers.get("Content-Type")).to.include("charset=utf-8");    
  });
  
  // 15. ASSERTIONS DLA GRANICZNYCH WARTOŚCI  
  pm.test("Integer fields are within reasonable bounds", function () {  
  pm.expect(jsonData.zupaid).to.be.at.most(999999);  
  pm.expect(jsonData.danieid).to.be.at.most(999999);  
  pm.expect(jsonData.nameNo).to.be.at.most(999999);  
  pm.expect(jsonData.number).to.be.at.most(999999);  
  });
