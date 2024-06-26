## Вопросы:
1. Разница между TCP и UDP.
2. TCP ожидает только подтверждения клиента о принятии пакета? Как узнает что пакет целый (Есть такая проверка)?
3. Как сделано подтверждение в TCP передачи данных и гарантия их сохранности?
4. Какой-нибудь http заголовок сможешь назвать, который постоянно используется? Именно в протоколе - a header

## 1. Разница между TCP и UDP.

TCP (Transmission Control Protocol) и UDP (User Datagram Protocol) - это два основных протокола передачи данных в компьютерных сетях. Они оба используются для передачи данных через сеть, но имеют несколько существенных различий:

1. Надежность передачи данных:
   - TCP обеспечивает надежную передачу данных. Это означает, что он гарантирует, что данные будут доставлены в целости и сохранности, а также в правильном порядке. Если пакет данных потеряется или повреждится в процессе передачи, TCP обеспечивает его повторную отправку.
   - UDP, напротив, является протоколом без установления соединения и не обеспечивает надежную доставку данных. Он отправляет пакеты данных без каких-либо гарантий относительно доставки, порядка или целостности.

2. Управление соединением:
   - TCP устанавливает соединение между отправителем и получателем перед передачей данных и обеспечивает управление этим соединением. Он гарантирует, что данные будут доставлены и в правильном порядке.
   - UDP не устанавливает соединение и не имеет механизмов управления этим соединением. Он просто отправляет пакеты данных и не заботится о дальнейшей обработке.

3. Затраты на передачу данных:
   - Из-за своей надежности и механизмов управления соединением, TCP имеет большие накладные расходы на передачу данных, чем UDP. Это связано с установкой соединения, подтверждениями, контролем потока и т.д.
   - UDP имеет меньшие накладные расходы, так как он просто отправляет пакеты данных без дополнительных проверок и управления.

Итак, TCP обеспечивает надежность и управление соединением, подходящий для приложений, где важна целостность и порядок данных (например, веб-сайты, электронная почта). UDP, с другой стороны, предоставляет более быструю и менее надежную доставку, что подходит для приложений, где небольшие задержки важнее, чем надежность (например, потоковое видео, онлайн-игры).

## 2. TCP ожидает только подтверждения клиента о принятии пакета? Как узнает что пакет целый (Есть такая проверка)?

TCP обеспечивает надежную передачу данных. Это означает, что он гарантирует, что данные будут доставлены в целости и сохранности, а также в правильном порядке. Если пакет данных потеряется или повреждится в процессе передачи, TCP обеспечивает его повторную отправку.

## 3. Как сделано подтверждение в TCP передачи данных и гарантия их сохранности?

TCP обеспечивает надежную передачу данных путем использования механизма подтверждения (acknowledgment). Когда отправитель посылает пакет данных, он ожидает подтверждения от получателя о том, что данные были успешно получены. Если подтверждение не получено в течение определенного времени, отправитель повторно отправляет пакет. Этот процесс гарантирует сохранность данных и правильный порядок их доставки. Таким образом, TCP обеспечивает надежную передачу данных путем повторной отправки потерянных или поврежденных пакетов и использования подтверждений для гарантии доставки данных в целости и сохранности.

## 4. Какой-нибудь http заголовок сможешь назвать, который постоянно используется? Именно в протоколе - a header

Несколько примеров HTTP заголовков, которые часто используются:

Заголовок `('Content-Type', 'application/json')` используется для указания, что контент запроса или ответа является JSON-данными. Он помогает клиенту и серверу правильно интерпретировать передаваемые данные.

- `Content-Type`: указывает тип контента в теле запроса или ответа, например, `application/json`.
- `Authorization`: содержит информацию для аутентификации пользователя, обычно используется для передачи токена доступа.
- `User-Agent`: содержит информацию о браузере или приложении, отправляющем запрос.
- `Accept`: указывает, какие типы контента клиент может принять от сервера.

Пример создания заголовков в JS коде:

```javascript
const headers = new Headers();
headers.append('Content-Type', 'application/json');
headers.append('Authorization', 'Bearer token123');

fetch('https://api.example.com/data', {
    method: 'GET',
    headers: headers
})
.then(response => {
    // Обработка ответа
})
.catch(error => {
    // Обработка ошибки
});
```

В этом примере создаются и добавляются HTTP заголовки `Content-Type` и `Authorization` к запросу, отправляемому с помощью функции `fetch` в JavaScript.
