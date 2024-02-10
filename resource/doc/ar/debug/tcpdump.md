# اصطياد الشبكة

في المثال التالي، سنقوم باستخدام ```tcpdump``` لمشاهدة البيانات التي تم نقلها عبر ```websocket``` لتطبيق ```workerman-chat```. في مثال ```workerman-chat```, يعمل الخادم على توفير خدمة ```websocket``` من خلال المنفذ ```7272```. لذا سنقوم بالتقاط حزم البيانات التي تم نقلها عبر المنفذ ```7272```.

1. *تشغيل الأمر* ```tcpdump -Ans 4096 -iany port 7272```.

2. أدخل ```http://127.0.0.1:55151``` في شريط عنوان المتصفح.

3. أدخل اسم مستعار ```mynick```.

4. في مربع النص، أدخل ```hi, all ！```.

*البيانات المقتنصة في النهاية كالتالي:*

```plaintext
/*
 * مصافحة TCP الأولى
 * المنفذ المحلي 60653 يرسل حزمة SYN إلى المنفذ البعيد 7272
 */
17:50:00.523910 IP 127.0.0.1.60653 > 127.0.0.1.7272: Flags [S], seq 3524290970, win 32768, options [mss 16396,sackOK,TS val 28679666 ecr 28679554,nop,wscale 7], length 0
E..<.h@.@.HQ...........h..i..........0....@....
............

...

/*
 * مصافحة TCP الثانية
 * المنفذ البعيد 7272 يرد بحزمة SYN+ACK إلى المنفذ المحلي 60653
 */
17:50:00.523935 IP 127.0.0.1.7272 > 127.0.0.1.60653: Flags [S.], seq 692696454, ack 3524290971, win 32768, options [mss 16396,sackOK,TS val 28679666 ecr 28679666,nop,wscale 7], length 0
E..<..@.@.<..........h..)I....i......0....@....
............

...

/* 
 * اتفاقية مصافحة TCP الثالثة، اكتمال الاتصال TCP
 * المنفذ المحلي 60653 يرسل حزمة ACK إلى المنفذ البعيد 7272
 */
17:50:00.523948 IP 127.0.0.1.60653 > 127.0.0.1.7272: Flags [.], ack 1, win 256, options [nop,nop,TS val 28679666 ecr 28679666], length 0
E..4.i@.@.HX...........h..i.)I.......(.....
........

...

/*
 * مصافحة websocket
 * المنفذ المحلي 60653 يرسل بيانات طلب مصافحة websocket إلى المنفذ البعيد 7272
 */
17:50:00.524412 IP 127.0.0.1.60653 > 127.0.0.1.7272: Flags [P.], seq 1:716, ack 1, win 256, options [nop,nop,TS val 28679666 ecr 28679666], length 715
E....j@.@.E............h..i.)I.............
.......GET / HTTP/1.1
Host: 127.0.0.1:7272
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux i686; rv:31.0) Gecko/20100101 Firefox/31.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-cn,zh;q=0.8,en-us;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Sec-WebSocket-Version: 13
Origin: http://127.0.0.1:55151
Sec-WebSocket-Key: zPDr6m4czzUdOFnsxIUEAw==
Cookie: Hm_lvt_abcf9330bef79b4aba5b24fa373506d9=1402048017; Hm_lvt_5fedb3bdce89499492c079ab4a8a0323=1403063068,1403141761; Hm_lvt_7b1919221e89d2aa5711e4deb935debd=1407836536; Hm_lpvt_7b1919221e89d2aa5711e4deb935debd=1407837000
Connection: keep-alive, Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket

...
```

‍*الرموز في البيانات:*
ّ
`[S]` تعني طلب `SYN` (بدء طلب الاتصال)؛ 
`[.]` تعني رد `ACK`، وتشير إلى أن النظام البعيد قد تلقى الطلب؛ 
`[P]` تعني إرسال البيانات؛ `[P.]` تعني `[P]` + `[.]`

*إذا كانت البيانات التي تم نقلها على المنفذ هي بيانات ثنائية، فيمكن استخدام العرض السادس عشر لرؤيتها.* `tcpdump -XAns 4096 -iany port 7272`
