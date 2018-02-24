# yml2kong
Take a yml config and turn it into the relevant requests to ensure that Kong's configuration matches the definition

## Usage: 

```
sh kong-configure.py /path/to/configuration.yml
```

## Example yml config: 

```
---
consumers:
  - username: Admin
    keys:
      - type: key-auth
        value: ...      
    set: # set a variable:
      - admin_user_id: result.id
  - username: exampleoauthclient
    custom_id: exampleoauthclient
    set: # set a variable:
      - example_oauthclient_id: result.id
    
apis:
  - name: admin'  
    uris: /manage' 
    upstream_url: http://127.0.0.1:8001'
    plugins:
      - name: key-auth    
  - name: mockbin
    hosts: /mockbin 
    # uris: mockbin.example.com,foo.example.com
    upstream_url: http://mockbin.org
    
# global:
plugins: 
  - name: oauth2
    create_anon_user: true
    config:
      enable_password_grant: true
      anonymous: "{{anon_user_id}}"
      global_credentials: true
    apps: # /consumers/:consumer/oauth2
      name: exampleApp
      consumer: {{example_oauthclient_id}}
      client_id: .. # nice if we could have a generate_key() function
      client_secret: .. # nice if we could have a generate_key() function
      redirect_url: "https://example.com"
  - name: http-log
    config:
      http_endpoint=http://firehose
      method=POST
```
