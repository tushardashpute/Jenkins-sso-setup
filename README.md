# Configure Azure Active Directory with Jenkins

Pre-requisites:

- You need to be an Azure admin or have the admin rights to add the API permissions required for this setup.
- Your Jenkins server has enabled HTTPS
  
1- Initial Jenkins setup

Go to Manage Jenkins then Manage Jenkins, and find Azure AD plugin. Once installed, you need to restart your Jenkins.

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/af56a620-8508-4382-8e48-72b265b48e5c)

2. Create ALB and attach target group to the jenkins instance.


![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/0b2f5c2f-06fd-49f0-ad02-238a098f1de3)

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/2a2e95e8-6ddd-487b-9f6f-afa8fdc7e6f6)

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/118e0a2a-6418-47cc-88ec-4400ad9d67ff)

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/47cce919-d24a-403f-a79d-861666f09d6f)


3- Azure AD setup

-  Go to your Azure Portal and select Azure Active Directory, then click on App registrations, then New registration

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/12a6f5c0-31ed-4b7f-98ad-70beab3f798c)

                                    AAD > App registrations


- Give a name to your application. If you have many Jenkins running, name your Jenkins accordingly (see above) so you can make a difference between the different settings.
- Choose who can use the application. In a private IT environment, you should only allow your AAD (single tenant) tenant to be used.
- Choose Web and finally register your application. To choose web application we must jenkins url of the type https.

  ![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/b9835377-f1d4-499f-9f65-bd9e94f6f968)

- In the application page, go to Authentication and add the URI of your Jenkins instance. If your Jenkins URI is internal, just put the internal URI that your internal DNS can resolve.

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/729c7c4c-9f48-480a-b79b-f3afd0b2a018)
![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/f0b40c07-fdcd-445a-963e-72e385261ff1)

- Select ID tokens and make sure you have selected a single tenant in the supported account types section.
- Go to API permissions, and add the following permissions:

      Microsoft Graph: Directory.Read.All / Delegated
      
      Microsoft Graph: Directory.Read.All / Application
      
      Microsoft Graph: User.Read / Delegated

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/5a55e297-b77e-44d6-a7bc-8f3f0d22cc37)
            AAD > App registrations > your Jenkins app > API permissions

- Click on Grant admin consent for your company tenant
- Go to Certificates & secrets and generate a new client secret.

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/6d1abf44-bffe-42cd-bd06-5dbdd3f5be98)

**Note: the secret is visible only once. Copy it and keep in your notes to finalize the Jenkins setup. In addition to the secret, note the Application ID (Client ID) and Directory ID (Tenant)**

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/8cf17e81-7b53-4acd-b1c3-e0833f3d9797)

Now, your Azure AD is setup.

3- Finalize the Jenkins setup

- Go to your Jenkins, Manage Jenkins, and then Configure Global Security.
- In the Authentication section, choose Azure Active Directory within the Security Realm bloc.
- From the notes above, insert the Client ID, Client Secret, and Tenant. Click on Apply (do not save yet! You can lock yourself if your settings are wrong)
- In order to make sure your settings are fine, in the Authorization section, select Azure Active Directory Matrix-based security, and type a username or group name that exists in your AAD.

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/00362e0a-c2da-4f43-a74a-f191ba676436)
![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/dfbbf20c-1b61-4bcd-9432-5a7d6abd4d33)

If you face any issues with the security, you can always go to your config.xml file and disable the security realm by following these steps:

1. Stop Jenkins
2. Go to $JENKINS_HOME in the file system and open config.xml.
3. Look for the false element in this file.
4. Replace true with false
5. Remove the elements authorizationStrategy and securityRealm
6. Start Jenkins

At this step, your Jenkins is in the unsecured mode where everyone gets full access. Fix the issue and enable again the authorizationStrategy and securityRealm.

![image](https://github.com/tushardashpute/Jenkins-sso-setup/assets/74225291/7fddbde8-aa6a-4261-aaa6-7da20a866415)
