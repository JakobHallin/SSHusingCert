SSH using SSH cert
//raspberry pi

1 create the CA for the ssh SSH dont use the x.509 certificate  but use the SSH certificate

ssh-keygen -t rsa -b 4096 -f CA

2 now I have the CA I need to add it to the Client/target i want to use remote acces with certificate to acces

I will use SSH with just password for now on local network

2.1 place where i will place the CA public certificate

    mkdir SSHtest 
    scp -r ./CA.pub pi@192.168.1.12:/home/pi/SSHtest 
    now i have it inside the raspberrypi

3 inside the raspberry i will need to changes
3.1 add the CA.pub certificate
    sudo cp CA.pub /etc/ssh/CA.pub
3.2 change permisson to make sure root is owned by root (user, group)
    sudo chown root:root /etc/ssh/CA.pub
    sudo chmod 644 /etc/ssh/CA.pub

4 eddit the SSH configuration

    sudo nano /etc/ssh/sshd_config
    inside change
    TrustedUserCAKeys /etc/ssh/CA.pub
    make so only certificate work
    PasswordAuthentication no
    PubkeyAuthentication yes
    PermitRootLogin no

    remove UsePAM yes
5 restart ssh
    sudo systemctl restart ssh
6 now we cant loging using the password we  get  Permission denied (publickey).
<img width="663" height="64" alt="Screenshot from 2025-10-03 15-31-34" src="https://github.com/user-attachments/assets/f536dfeb-c1c6-433b-8e47-bc3483a74d93" />

7 now with the CA we should be create certs to login and use the raspberry pi
    the point is to generate with experation date NOTE CA JOB TO SET THE experation date
    ssh-keygen -t rsa -b 4096 -f ./user crate user cert
    <img width="786" height="339" alt="Screenshot from 2025-10-03 15-35-02" src="https://github.com/user-attachments/assets/e3298930-0f4d-4c91-a80e-9f520fa4349e" />


Sign it
ssh-keygen -s CA -I user -n user -V +1h user
we get user-cert.pub that is signed by the CA



7 now it should work 
    ssh-keygen -s CA -I user-cert -n pi -V +1h user.pub
    important is to use -n to the right user other wise it wont work

<img width="680" height="117" alt="Screenshot from 2025-10-03 15-48-44" src="https://github.com/user-attachments/assets/18606b82-e9ba-4ac0-91ca-4b1fe212c21f" />

    


8 future 
    set it up to expose to internet 
    automate the certificate rollout
    
    
