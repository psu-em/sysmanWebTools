This repo holds the SysMan Python Tools in a Docker container.

Create a 'besapi.conf' file in the repo app folder with the following:

""""""""""""""
[besapi]
BES_ROOT_SERVER = https://bigfix.server.com
BES_USER_NAME = u3er
BES_PASSWORD = pa$$word

[snow]
SNOW_USER = u3er
SNOW_PASSWORD = pa$$word

[sysmantools]
group_password = pa$$word
auth_token = UUID_token

[server]
encryptedString = UUID_token

[auth]
# Shared password to access webservice
encryptedString = encryptedpassword
salt = SALT
passphrase = PASSPHRASE
# used for securely signing the session cookie
secret_key = secret_key
# API token for accessing webservice
auth_token = token

[users]
# usernames = servicenow_token
userA = ""
userB = "" 

[redis]
rq_dashboard_redis_url = redis://acb@IP_ADDRESS:6379/0
rq_dashboard_username = sysman
rq_dashboard_password = $ysMan

[teams]
softwareupdateapi = teams_webhook
servicechangesapi = teams_webhook

[codedefaults]
development = True

""""""""""""""
The encryptedstring contains the GenerateEncryptedString() using salt and passphrase. This can be generated in the pysysmantools.ipynb.

Create SSL certificates for your server and put them into nginx/ssl/

Rename the nginx/example_default.conf to nginx/default.conf and update the MYSERVER.COM & SERVER_IP.

Rename the example_docker-compose.yml to docker-compose.yml and update MYSERVER.COM.

To build the Docker container, cd into the repo and run 'sudo ./start.sh'

Logs output to the Logs folder

To enable debug mode, forcing the tool to post to the 'SysMan API Test' yammer group, change the development variable in the besapi.conf to True:

    [codedefaults]
    development = True

To get any errors with the Python code that are not in the python logs, use:

    docker logs -f publishsoftware
    
Known Errors

1. SSL Warnings.

We are getting a lot of errors for InsecureRequests...we should try to verify the Certificate.
      InsecureRequestWarning,
    /usr/local/lib/python3.6/site-packages/urllib3/connectionpool.py:1020: InsecureRequestWarning: Unverified HTTPS request is being made to host 'bigfix.server'. Adding certificate verification is strongly advised. See: https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
    
2. API Access Blocked

Occasinally, the network seems to reject api requests due to traffic overloads. Wait a min... ¯\_(ツ)_/¯

    requests.exceptions.SSLError: HTTPSConnectionPool(host='bigfix.server', port=52311): Max retries exceeded with url: /api/query?relevance=(name%20of%20site%20of%20it,%20type%20of%20it,%20modification%20time%20of%20it,%20name%20of%20it,%20id%20of%20it)%20of%20bes%20fixlets%20whose%20(name%20of%20site%20of%20it%20contains%20%22SysManDev%22%20and%20(modification%20time%20of%20it%20%3E%20now-24*hour))%0A (Caused by SSLError(SSLError(1, '[SSL: DECRYPTION_FAILED_OR_BAD_RECORD_MAC] decryption failed or bad record mac (_ssl.c:2309)'),))

## Future Deveopment?
Database Design
- Store which tasks are published, when, family name, site published to, security update, self-service target, 

Reporting
- How may tasks are published each day, each week, month.
- How many tasks are securtiy updates
- Tasks to offer to self-service automatically