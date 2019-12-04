---
title: "자바 XML TO JSON 변환 하기"
date: 2019-03-22T14:21:02+08:00
---

# XML TO JSON (AND CUSTOM OBJECT)

xml 데이터를 json으로 변환하기 위한 작업이 필요하다.

그러기 위해서 spring framework에서 많이 사용하는 jackson(jackson-dataformat-xml)을 사용해 변환을 해보려 했다.

하지만 jackson 라이브러리를 사용해 xml을 json으로 변환할 때 xml의 multiple child 값들이 다 사라지면서 단일 값이 남게 된다.

그래서 찾아봤다.

똑같은 현상을 가지고 있는 사람을 발견했다.

[스택오버플로우](https://stackoverflow.com/questions/36410922/jackson-xml-to-json-converter-removes-multiple-child-records)

거기에 있는 답변을 참고해보자.

1. org.json 라이브러리를 추가시킨다.

2. org.json 라이브러리와 jackson 라이브러리를 섞어 사용한다.

예제 샘플이다
```
import org.apache.commons.io.IOUtils;
import org.json.JSONObject;
import org.json.XML;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;
...
try (InputStream inputStream = new FileInputStream(new File(
                "source.xml"))) {
    String xml = IOUtils.toString(inputStream);
    JSONObject jObject = XML.toJSONObject(xml);
    ObjectMapper mapper = new ObjectMapper();
    mapper.enable(SerializationFeature.INDENT_OUTPUT);
    Object json = mapper.readValue(jObject.toString(), Object.class);
    String output = mapper.writeValueAsString(json);
    System.out.println(output);
}
...
```
이렇게 하니까 Json 값이 나오게 되며 Object에 값들이 들어가게 된다.

여기서 커스텀 된 오브젝트를 사용하고 싶다면 Object 자리에 xml -> json으로 컨버터된 custom object를 넣어주면 된다.

그러면 필요한 xml을 custom obeject안에 담을 수 있게 된다.