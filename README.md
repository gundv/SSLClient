# SSLClient
## Server Setup ##
## CA Certificate Setup For SSLClientVerify require ##
### Step 1 ###
Using command: 
    
        curl https://your_url -v
        
Result will shown:
    
        * Rebuilt URL to: https://your_url/
        *   Trying ...
        * TCP_NODELAY set
        * Connected to your_url (YOUR_IP_ADDRESS) port 443 (#0)
        * ALPN, offering h2
        * ALPN, offering http/1.1
        * Cipher selection: ALL:!EXPORT:!EXPORT40:!EXPORT56:!aNULL:!LOW:!RC4:@STRENGTH
        * successfully set certificate verify locations:
        *   CAfile: /etc/ssl/cert.pem
        
Remote to server and check for Certificate Authority (CA) inside  "/etc/ssl/" exist or not.
    
If there is CA certificate match with the CA we got from Your Provider SKIP this step.
    
In current example, there was no such file "/etc/ssl/cert.pem" existed, as the result, we manually copied it.

### Step 2 ###

Editing the apache2 conf file for respective domain
In conf file add 

        #Your Server Should have this config

        SSLEngine on #We enable SSL to our site 
        SSLVerifyClient none  #Certificate Verification for the client (require for Mobile App) 
        SSLVerifyDepth  10 #Dept of the CA certificate written in server or client certificate
        SSLCACertificatePath /etc/ssl/ #The Directory that we obtain from Step 1
        SSLCertificateFile /web_server_config_path/server.crt #Server SSL certificate from SSL provider
        SSLCertificateKeyFile /web_server_config_path/server.crt.key #Server SSL certificate private key
        SSLCertificateChainFile /web_server_config_path/chained_ca_server.crt #chained certificate From subRoot CA and Root CA for server.crt
              
        
        
        #Start Client Auth Config
        <Directory /web_server_public_html_path/directory_you_want_to_protect> #Protect Whole Directory
            SSLVerifyClient require
            SSLVerifyDepth  10
        </Directory>
        <Location /path_to/protected_api> #Protect specific path
            SSLVerifyClient require
            SSLVerifyDepth  10
        </Location>
         #End Client Auth Config
        
   

After complete this step make sure to restart apache server. This is the whole process of server configuration.


