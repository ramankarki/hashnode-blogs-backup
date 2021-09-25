## What is SSH | Why it is useful | How to setup SSH keys on GitHub and Bitbucket

Usually, working on our project, we clone our repository using **HTTPS**. Something like this,
```
raman@raman-Inspiron-15-3552:~/Documents$ git clone https://github.com/ramankarki/portfolio.git
Cloning into 'portfolio'...
remote: Enumerating objects: 586, done.
remote: Counting objects: 100% (586/586), done.
remote: Compressing objects: 100% (392/392), done.
remote: Total 586 (delta 263), reused 512 (delta 192), pack-reused 0
Receiving objects: 100% (586/586), 4.59 MiB | 2.05 MiB/s, done.
Resolving deltas: 100% (263/263), done.
raman@raman-Inspiron-15-3552:~/Documents$
```

We make changes and try to push our commits like this,

```
raman@raman-Inspiron-15-3552:~/Documents/portfolio$ git push
Username for 'https://github.com':
``` 

### The problem:
Every time we try to push our code, we have to enter our username and password. And that can be frustrating while we are working on our project for long hours.

### The solution:
If you didn't notice, let me tell you.
There are two ways of cloning your repository. 
- **HTTPS** - This is what you saw above.
- **SSH** - This is our solution to this problem. We setup once and never have to enter our credentials.


![screen shot of github.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1632570584766/ugqk_5xBR.png)

## What is SSH?
Secure Shell (SSH) is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line, login, and remote command execution, but any network service can be secured with SSH.

![ssh authentication.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1632572662306/hu4MN9Q-a.png)

So now we know about SSH and its use case.

## Let's setup our SSH keys locally.
To begin with, we create SSH keys in our terminal. Enter `ssh-keygen -C "yourmail@gmail.com"` and just press enter on each prompt you get.
```
raman@raman-Inspiron-15-3552:~$ ssh-keygen -C "yourmail@gmail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/raman/.ssh/id_rsa): 
Created directory '/home/raman/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/raman/.ssh/id_rsa
Your public key has been saved in /home/raman/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:UZ4+fPyPB1MMh7ko2Z6GErZkHusEF5/PbDM2IjltcP0 raman@raman-Inspiron-15-3552
The key's randomart image is:
+---[RSA 3072]----+
|          .    o |
|        .o .  + .|
|        .oo= . = |
|      . O+*.+ . o|
|       BSX+Boo . |
|        X =o&.E  |
|       o = = +.o |
|        .      o.|
|              ...|
+----[SHA256]-----+
raman@raman-Inspiron-15-3552:~$
```

By default your private key will be saved in '**.ssh/id_rsa**' file and public key in '**.ssh/id_rsa. pub**'.

With SSH keys, if someone gains access to your computer, they also gain access to every system that uses that key. To add an extra layer of security, you can add a passphrase to your SSH key. A passphrase is similar to a password. But in our case, we didn't add any passphrase. It's ok if our device won't be used by anyone. But if you are working on a team, you should definitely add one.

## Now we setup SSH keys on GitHub
- Just go to your **GitHub settings** page and move to [**SSH and GPG keys tab**](https://github.com/settings/keys)

*Page looks something like this,*

![github settings page.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1632572965200/63rbvB6p5.png)

- Click on the **New SSH key** button
- Move to your terminal and enter `cat .ssh/id_rsa.pub`
```
raman@raman-Inspiron-15-3552:~$ cat .ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDDMH4lNfHFGF0G0F7kYvrVOKjSMfzKwYb+gttcSUQbbBSD/3xq3XkduZkx6eQ+j6HtBMbAV0uqO2VQDK7XIXM2mvn7btKM7oQDBftGX1Xtrw8oE3UnUiorQVZkOlF76ApX5xgLFr1eT6QTFhdgdGT6s3yw1zNIa/cjia9j29P7tnNy7voUyLdBUVG8S6sRI9SC229AFP3XEyp7sefydTfI/cEJXcEBbEG71YNhkCqHnywALoTrq+PztpdZlZu+SVeidaIK76rz7Iqta/TnfymYvhqs830eGNE5hbHYjnZELyViRnFWeLAlzXa3zjCf0l62jMhKfz7lnOxPp5Go5We30rNWKgj5z1shEAcbp1L3iWQO+OJvghNT3RZBpqRm5sEwzNGNrH4V1r518QeQsPSSMVsXaoelyI7siKD2P4i3HFs5EjAtKmI1eVGGdugrZ2zVoAi+Moue7QGWNeTSz4Ks5TgeDdtrhVTqRpRnAFW7jaA3KGtnOUjgJBP8KW4u7vc= raman@raman-Inspiron-15-3552
raman@raman-Inspiron-15-3552:~$
```
*You should see a long random string, That's your public SSH key*
- Copy everything that appeared on your terminal, starting from 'ssh-rsa'
- Move to your browser (same GitHub settings page) and enter **Title** whatever you want it doesn't matter.
- Paste your public **SSH** key on **Key** field and click **Add SSH key** button.


*GitHub should have redirected you to enter your password for confirmation.*

And with that all done, Our SSH keys setup is complete. From today we clone our repository using SSH.

## Let's test it out 🥳🥳
We clone and push our commits using SSH.

- Go to your repository and choose **SSH** option for cloning.
```
raman@raman-Inspiron-15-3552:~$ git clone git@github.com:ramankarki/portfolio.git
Cloning into 'portfolio'...
The authenticity of host 'github.com (13.250.177.223)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com,13.250.177.223' (RSA) to the list of known hosts.
remote: Enumerating objects: 586, done.
remote: Counting objects: 100% (586/586), done.
remote: Compressing objects: 100% (392/392), done.
remote: Total 586 (delta 263), reused 512 (delta 192), pack-reused 0
Receiving objects: 100% (586/586), 4.59 MiB | 1.97 MiB/s, done.
Resolving deltas: 100% (263/263), done.
raman@raman-Inspiron-15-3552:~$
```
- Make some changes and try to push your code.
```
raman@raman-Inspiron-15-3552:~/portfolio$ git push
Warning: Permanently added the RSA host key for IP address '20.205.243.166' to the list of known hosts.
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 2 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 375 bytes | 375.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To github.com:ramankarki/portfolio.git
   c85c73f..348bbb1  master -> master
raman@raman-Inspiron-15-3552:~/portfolio$
```

*Your code should have pushed on GitHub without prompting you to enter you username and password.*

## Adding our same public SSH key on Bitbucket
- Go to your **Bitbucket settings page** and move to [**SSH keys tab**](https://bitbucket.org/account/settings/ssh-keys/).
- Add your public **SSH** key like you did on GitHub and clone your repository using **SSH**.

So, that's it on this topic. Hope you fixed your problem. 

Let me know if I missed something.

Thank you for reading this blog. ❤️❤️