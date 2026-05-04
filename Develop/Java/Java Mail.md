---
source_title: Java Mail
categories:
- Develop
- Java
last_modified: '2024-09-13T05:50:41Z'
---
用 Java 发送电子邮件，在 2018 年以前一般使用 javax.mail，可以使用 1.4.7 版本。引用的时候，还需要 javax.activation。(Sun 认可的标准扩展都以 javax 作为包名称开头。javax 中的包都只提供了接口，javax 中并不提供具体的实现。)
 ```
        
            javax.mail
            mail
            1.4.7
        
        
            javax.activation
            activation
            1.1.1
        
```

2018 年以后，javax.mail 更名为 jakarta.mail。 
 ```
        
            com.sun.mail
            jakarta.mail
            2.0.1
        
```

### Sample
 ```
public class MAIL {
    public static final String VERSION = "v1.0.0";
    private static String host, user, password;
    private static Properties mailSERV = new Properties();
    private static Session session;
    private static Transport ts;
    private static MimeMessage message;
    public MAIL(String sFile) {
        try {
            CNF cnf1 = new CNF(sFile);
            mailSERV = cnf1.get();
            host = mailSERV.getProperty("host");
            user = mailSERV.getProperty("user");
            password = mailSERV.getProperty("password");
            System.out.println(password);
            session = Session.getInstance(mailSERV);
            session.setDebug(true);
            ts = session.getTransport();
            ts.connect(host, user, password);
        } catch (Exception e) {
            log(e);
        }
    }
    public MAIL() {
        this("email.cnf");
    }
    public static int send(Map _mail) {
        String s_from, s_to, s_subject, s_content;
        List l_to;
        // default from = mail.user
        if (_mail.containsKey("from")) {
            s_from = _mail.get("from");
        } else {
            s_from = mailSERV.getProperty("user");
        }
        // to is list
        s_to = _mail.get("to").replace(";", ",");
        l_to = str2list(s_to, ',');
        s_subject = _mail.get("subject");
        s_content = _mail.get("content");
        try {
            message = new MimeMessage(session);
            message.setFrom(new InternetAddress(s_from));
            message.setSubject(s_subject);
            message.setContent(s_content, "text/html;charset=UTF-8");
            for (String to : l_to) {
                message.setRecipient(Message.RecipientType.TO, new InternetAddress(to));
                ts.sendMessage(message, message.getAllRecipients());
            }
            ts.close();
        } catch (Exception e) {
            log(e);
            return -1;
        }
        return 0;
    }
}
mail.transport.protocol=smtp
mail.smtp.auth=true
mail.smtp.starttls.enable=true
mail.smtp.ssl.protocols=TLSv1.2
host=
user=
password=
```
