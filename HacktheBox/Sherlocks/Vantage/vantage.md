# Vantage

<img width="100" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/vlogo.png" />

## DFIR - Very Easy

> ### Sherlock Scenario
> A small company moved some of its resources to a private cloud installation. The developers left the redirect to the dashboard on their web server. The security team got an email from the alleged attacker stating that the user data was leaked. It is up to you to investigate the situation.


- We download the required `.zip` containg two `.pcap` files:
  - `controller.2025-07-01.pcap`
  - `web-server.2025-07-01.pcap`

<img width="400" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/1.png" />

***

## Tasks:

### Task 1:
#### What tool did the attacker use to fuzz the web server ? (Format- include version e.g, nmap@7.80)

- In Wireshark, we can `control + f` to open the _display filter options_. From this point, we can specify that within `packet details` looking for a `String` that contains the word `fuzz`. This lists network packets containg the requested information, particularly the `User-Agent` tag, which in our case specifies:
> (Also known as just FFuF)

```
User-Agent: Fuzz Faster U Fool v2.1.0-dev\r\n
```

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/2.png" />

- **Flag for task:**
```
ffuf@2.1.0
```

***
### Task 2:
#### Which subdomain did the attacker discover?

- We start by applying a display filter to show only HTTP requests and responses:
```
http
```
- This displays all HTTP packets. For a more focused view, seperate requests and responses:
  - **Requests:** `http.request`
  - **Responses:** `http.response`

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/3.png" />


- The attacker discovers a valid subdomain when a request returns a **200 OK** (or any non-error) status. Filter for successful responses:

```
http.response.code == 200
```

- **Our full query:**

```
http and http.response.code == 200
```

- Now examine the packets in your filter results. We right-click on a 200 response and choose **Follow > TCP Stream**. This will show you the full HTTP conversation, including the request that triggered the response.

- Upon investigation with our filter, choosing a line reveals the `Full request URI`

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/4.png" />

- The full request URI:
```
http://cloud.vantage.tech/dashboard/header/
```

With this information, we can determine the attacker was able to find the `cloud` subdomain.

- **Flag for task:**
```
cloud
```

***
### Task 3:
#### How many login attempts did the attacker make before successfully logging in to the dashboard?

- Normally performing this we would want to filter our query to see POST traffic to locate login attempts:
```
http.request.method == "POST" and http.request.uri contains "/login"
```
- Since there isn't much traffic in our example, we can fine-tune our query to find the login endpoint. We apply a filter to see possible login requests:
```
http.request.full_uri contains"/login":
```
- From these requests we see a few POST requests.

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/5.png" />

- Further investigation into these packets reveals a `Form item` for each. We can see the credentials entered within each.

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/6.png" />

```
admin:StrongAdminSecret
root:root
demo:demo
```
- Finally before the user guessed the correct credentials and got redirected to `/Dashboard`

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/7.png" />

- The correct credentials:

```
admin:admin
```

- **Flag for task:**

```
3
```
***
### Task 4:
#### When did the attacker download the OpenStack API remote access config file? (UTC)


- To being investigation look for http requests that contain "/api":

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/8.png" />

```
http.request.uri contains "/api"
```

- After looking at the top-most packet we can see within the `Content-Disposition` tag:

```
admin-openrc.sh
```

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/9.png" />

- **Flag for task:**

```
2025-07-01 09:40:29
```
***
### Task 5:
#### When did the attacker first interact with the API on controller node? (UTC)

- From the previous task (downloading the OpenStack config file), we identify the IP address of the controller node.
- We assume the attacker's IP is `117.200.21.26` and the Controller IP is `157.230.81.229`
- With this information we move over to the `controller.2025-07-01.pcap` file and jump to the very first result with this filter:

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/10.png" />


```
http and ip.src == 117.200.21.26
```

- The first network packet Time contains the flag for this task:

- **Flag for task:**

```
2025-07-01 09:41:44
```
***
### Task 6:
#### What is the project id of the default project accessed by the attacker?

- From the previous task since we're already filtering for the attacker's IP on the controller node, we can then filter to check if any URI stem contains `project`.
- Further investigation reveals the project ID as the other network packets show the user merely navigating folders. The project ID is located within the URI stem.

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/11.png" />

```
Request URI: /identity/v3/projects/9fb84977ff7c4a0baf0d5dbb57e235c7/users/c373da67a62b48f393c45dc071fa80b8/roles/0501401642464242bcd799437b71bdc9
```

- We are able to see within the URI Stem that `9fb84977ff7c4a0baf0d5dbb57e235c7` appears to be the project ID.

- **Flag for task:**

```
9fb84977ff7c4a0baf0d5dbb57e235c7
```
***
### Task 7:
#### Which OpenStack service provides authentication and authorization for the OpenStack API?

- From the previous task, you can observe within the `User-Agent` tag:

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/12.png" />

```
User-Agent: openstacksdk/4.6.0 keystoneauth1/5.11.1 python-requests/2.32.4 CPython/3.13.5\r\n
```

- We see `keystoneauth1/5.11.1` indicating the program `keystone` is used:

- **Flag for task:**

```
keystone
```
***
### Task 8:
#### What is the endpoint URL of the swift service?

- We can `control + f` to search the `packet details` field for strings containing the word `swift`. After looking through a few logs on the web server we are able to locate the specific URL containg the swift service:

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/13.png" />

- We see that the swift endpoint is being used at the URL: `http://134.209.71.220:8080/v1/AUTH_9fb84977ff7c4a0baf0d5dbb57e235c7`

 **Flag for task:**

```
http://134.209.71.220:8080/v1/AUTH_9fb84977ff7c4a0baf0d5dbb57e235c7
```

***
### Task 9:
#### How many containers were discovered by the attacker?

- First on the controller.2025-07-01.pcap file, we look for a `GET` request that lists containers. In **OpenStack Swift**, listing containers is typically done with a `GET` to the account root, e.g., `GET /v1/AUTH_<project_id>`. The request will include an `X-Auth-Token` header (the attacker would have authenticated first).

- We can search for packets with the `GET` method and the path containing `/v1/`. For example, we use the display filter:

- We see only a handful of requests but the one that sticks out to us is:

```
http.request.method == "GET" && http.request.uri contains "/v1/AUTH_"
```

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/v14.png" />

```
GET /v1/AUTH_9fb84977ff7c4a0baf0d5dbb57e235c7?format=json HTTP/1.1\r\n
```

- We then dig a little deeper into investigation by right-clicking on the packet and clicking `Follow > HTTP stream` to see the full response. The response body will contain a JSON array (or plain text list) of container names.

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/v15.png" />

- From here, we see the line we're looking for:

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/v16.png" />

```
X-Account-Container-Count: 3
```

- This suggests that the swift service contains 3 containers.

 **Flag for task:**

```
3
```

***
### Task 10:
#### When did the attacker download the sensitive user data file? (UTC)

- From the last task, we can use the same packets to get our answer, as we see a GET request for:

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/v17.png" />

```
Request URI: /v1/AUTH_9fb84977ff7c4a0baf0d5dbb57e235c7/user-data/user-details.csv
```

- We can see from the `Time` column of this packet that this was performed at `2025-07-01 09:45:23`.

 **Flag for task:**

```
2025-07-01 09:45:23
```

***
### Task 11:
#### How many user records are in the sensitive user data file?

- From the previous task, we found the user attempting to download the sensitive user data file. From that, we can follow the HTTP stream:

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/v18.png" />

- Which within the response, we see the entire file and the user records in the file listed in order:

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/v19.png" />

- Counting, we see **28 user records.**

 **Flag for task:**

```
28
```

***
### Task 12:
#### For persistence, the attacker created a new user with admin privileges. What is the username of the new user? 

To find this information, we would examine the controller pcap for a POST request with "user" somewhere in the endpoint:

```
http.request.method == "POST" && http.request.uri contains "user"
```
<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/v20.png" />

- Following the HTTP stream, we find a request that gives us more information:

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/v21.png" />

- From this we can find that the user created an administrative user named `jellibean`.

 **Flag for task:**

```
jellibean
```

***
### Task 13:
#### What is the password of the new user? 

- From that same request we can also see the password:

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/v22.png" />

- User set their passphrase as `P@$$word`.

 **Flag for task:**

```
P@$$word
```

***
### Task 14:
#### What is MITRE tactic id of the technique in task 12?

- Based on the MITRE ATT&CK framework, the technique ID for an attacker creating a new account is T1136. Since the attacker created the account with administrator privileges in a cloud environment (OpenStack), the most appropriate sub-technique is **T1136.003 - Create Cloud Account**.

Here is how this action maps to the framework:

- Technique ID: T1136 (Create Account)
- Sub-technique: T1136.003 (Create Cloud Account)
- Tactic: Persistence (The attacker ensures they have a way to maintain access to the system)

>[!Note]
>
> ### Why this mapping?
> - **Primary Action:** The attacker's goal is to create a new user account on the compromised system . This is the core action of T1136.
> - **Context:** The attack took place in an OpenStack cloud environment. The T1136.003 sub-technique specifically covers the creation of accounts in cloud environments, which includes user accounts with specific roles and permissions.
> - **Privilege Escalation:** By granting the new user "admin privileges," the attacker is ensuring their new account has high-level access. This aligns with the description of T1136, where accounts may be created to establish secondary credentialed access
> 
> While the base technique ID **T1136** would be acceptable, **T1136.003** is the most precise answer given the cloud context. If the OpenStack environment were using a directory service like LDAP, it might also be mapped under **T1136.002 (Domain Account)** , but for a native cloud identity, T1136.003 is the best fit.
>
> #### References:
> 
> [T1136: Create Account](https://attack.mitre.org/versions/v7/techniques/T1136/)
> [T1136.003: Create Account: Cloud Account](https://attack.mitre.org/versions/v7/techniques/T1136/003/)

 **Flag for task:**

```
T1136.003
```

***

- This completes the Sherlock!

<img width="500" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Vantage/Images/vclear.png" />
