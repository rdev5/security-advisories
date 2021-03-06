
# Prey Anti-Theft application for Android missing server certificate validation


## 1. Advisory Information

**Title:** Prey Anti-Theft application for Android missing server certificate validation

**Advisory ID:** STIC-2014-0731

**Advisory URL:** [http://www.fundacionsadosky.org.ar/publicaciones/#seguridad-en-tic](http://www.fundacionsadosky.org.ar/publicaciones/#seguridad-en-tic)

**Date published:** 2014-11-11

**Date of last update:** 2014-11-11

**Vendors contacted:** Prey

**Release mode:** Coordinated release



## 2. Vulnerability Information

**Class:** Improper Following of a Certificate's Chain of Trust [[http://cwe.mitre.org/data/definitions/296.html](http://cwe.mitre.org/data/definitions/296.html)]

**Impact:** Data loss

**Remotely Exploitable:** Yes

**Locally Exploitable:** Yes

**CVE Identifier:** N/A



## 3. Vulnerability Description

Prey is an anti-theft application that lets smartphone owners track and locate lost or stolen devices. It provides accurate geolocation of a missing device and allows users to remotely lock it, take pictures, play alarm sound or display onscreen messages. The application features can be controlled from the Prey project's website or via SMS. As of September,2014 the application had between 1 to 5 million installations worldwide[1].
 
Although communication between the Prey application running on an Android device and the controlling web server is performed over HTTPS, the former does not validate the SSL certificate presented by the latter. As a result it is possible to completely subvert the anti-theft protection of Prey. To do so, an attacker simply needs to perform a Man-in-the-Middle attack on the communications between the Prey app running in the device (presumably stolen locked with an user provided password) and the web server, present a fake server certificate and send a _lock command_ with a password of the attacker's choosing to the device. The attacker can then unlock the device manually with his/her provided password. Other types of attacks are possible since all communications between the device and the website can be inspected and modified by the attacker.


## 4. Vulnerable packages

* Prey Anti-theft fo Android version 1.1.3 and below.

## 5. Vendor Information, Solutions and Workarounds

The vendor acknowledged the problem and committed to publish a new version of the application fixing the issue by November 11th, 2014. 


## 6. Credits

This vulnerability was discovered and researched by Joaquín Manuel Rinaudo. The publication of this advisory was coordinated by Programa Seguridad en TIC. 

## 7. Technical Description

The vulnerability is found in _com.prey.net.HttpUtils_ class which instantiates an HttpClient to connect to Prey's server. The HttpClient uses a custom SSLSocketFactory named EasySSLSocketFactory to obtain socket objects used to communicate with the server. This class also call the method _setHostnameVerifier(SSLSocketFactory.ALLOW_ALL_HOSTNAME_VERIFIER)_ to accept as valid any hostname presented in the server certificate[2]. Furthermore, since the EasySSLCocketFactory implements a _X509TrustManager_ with empty verifier methods [3], any certificate presented by the server is considered valid by the application. This allows an attacker to mount a MITM attack to impersonate the Prey panel server with self-made X509 certificate.

To unlock a stolen device, the attacker needs to spoof the lock command specifying a new password to gain control of the device. This could be done by modifying the server's response to the device request for commands at _https://solid.preyproject.com/api/v2/devices/[DEVICE_ID].json_ to:


```
  [
      {
          "command": "start", 
          "options": {
              "unlock_pass": "easy"
          }, 
          "target": "lock"
      }
  ]

```

The application tries to obtain new commands from the server by registering to listen multiple android events such as changes in connectivity, battery level, accessing the airplane mode and even turning on and off the device.  


## 8. Report Timeline

* **2014-09-17:** 
          Request for security contact info filed in support page on the Prey project's website.
        
* **2014-09-23:** 
          The vendor team asks Programa Seguridad en TIC to send the vulnerability report via unencrypted email to security@preyproject.com.
        
* **2014-10-01:** 
          Technical details sent to the vendor.
        
* **2014-10-25:** Programa Seguridad en TIC requested an status update about the issue and communicated an estimated release date of the advisory by the 27th of October, 2014. Vendor requested to push back the release due to an internal re-organization of the company.
        
* **2014-10-27:** Programa Seguridad en TIC accepted to delay the advisory but only on the basis in receiving details about the status of the issue and a date commitment to release an updated version which fixes the problem. 
        
* **2014-10-28:** 
         Vendor informed that a patch was already developed and requested for advise as to how to avoid exposing clients running older versions to explotation of the vulnerability.  
        
* **2014-10-29:** Programa Seguridad en TIC asked the vendor to send a copy of the patch so it could then confirm the security issue was addressed. The vendor was advised to inform the users about the vulnerability and the risk involved so clients would be encouraged to update the application so as to minimize the vulnerability impact.
        
* **2014-10-30:** 
          Vendor sent the patched version  of the application to the researcher and notified that the modification consisted in changing the HostNameVerifier from _ALLOW_ALL_HOSTNAME_VERIFIER_ to _STRICT_HOSTNAME_VERIFIER_.
        
* **2014-11-3:** Programa Seguridad en TIC informed the vendor that the patch did not fix the problem since the application was still not verifying the certificate chain and that the root CA was a valid one from the Android CA store because they were using an empty TrustManager. Vendor was also notified that the advisory would be published on November, 10th.
.        
* **2014-11-10:** 
          Vendor acknowledged the problem and informed that an update would be available in Google Play store .
        

## 9. References

[1] [https://play.google.com/store/apps/details?id=com.prey](https://play.google.com/store/apps/details?id=com.prey)

[2] [https://github.com/prey/prey-android-client/blob/master/src/com/prey/net/HttpUtils.java](https://github.com/prey/prey-android-client/blob/master/src/com/prey/net/HttpUtils.java)

[3] [https://github.com/prey/prey-android-client/blob/master/src/com/prey/net/EasySSLSocketFactory.java](https://github.com/prey/prey-android-client/blob/master/src/com/prey/net/EasySSLSocketFactory.java)

## 10. About Fundación Dr. Manuel Sadosky

The Dr. Manuel Sadosky Foundation is a mixed (public / private) institution whose goal is to promote stronger and closer interaction between industry and the scientific-technological system in all aspects related to Information and Communications Technology (ICT). The Foundation was formally created by a Presidential Decree in 2009. Its Chairman is the Minister of Science, Technology, and Productive Innovation of Argentina; and the Vice-chairmen are the chairmen of the country’s most important ICT chambers: The Software and Computer Services Chamber (CESSI) and the Argentine Computing and Telecommunications Chamber (CICOMRA). For more information visit: [http://www.fundacionsadosky.org.ar](http://www.fundacionsadosky.org.ar)

## 11. Copyright Notice

The contents of this advisory are copyright (c) 2014 Fundación Sadosky and are licensed under a Creative Commons Attribution Non-Commercial Share-Alike 4.0 License: [http://creativecommons.org/licenses/by-nc-sa/4.0/](http://creativecommons.org/licenses/by-nc-sa/4.0/)
