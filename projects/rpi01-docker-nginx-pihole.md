# Pi-hole & Nginx Proxy Manager with Docker

## 📝 Overview  
This project documents my setup of Pi-hole as a DNS server alongside Nginx Proxy Manager (NPM) for reverse proxy management, both installed as containers using Docker.

## 🔧 Setup Details  
### **1. System & Software**  
- **Hardware:** Raspberry Pi Zero 2 W, 5V micro-usb power supply/PoE hat for Pi (I used a PoE hub hat from waveshare on Amazon), reliable microSD card (I went overkill with a Samsung 256gb), computer to use Raspberry Pi Imager
- **OS:** Debian-based 32-bit OS  
- **Software:** Docker, Pi-hole, Nginx Proxy Manager  

### **2. Installation Steps**  
#### **Install Raspberry Pi OS Lite (32-bit) to microSD card**
Navigate to Raspberry Pi's Imager site (https://www.raspberrypi.com/software/) to download the version compatible with your OS. In my case that is Windows

![Screenshot 2025-01-29 213705](https://github.com/user-attachments/assets/26ddfa1c-3fe7-4f61-8d26-d3ab73fa65db)

Once Imager is installed, make sure microSD card is connected to computer/device, open the program and you should see a screen like below

![Screenshot 2025-01-29 214155](https://github.com/user-attachments/assets/7bddf448-4601-4ce8-be58-4aa3006bdafb)

Select device being used (Raspberry Pi Zero 2 W in my case)

![Screenshot 2025-01-29 214617](https://github.com/user-attachments/assets/928a32de-650e-4d9d-8769-89280c99b6f4)

A few options down in the OS menu, select Raspberry Pi OS (other), description: "Other Raspberry Pi based images"

![Screenshot 2025-01-29 214900](https://github.com/user-attachments/assets/ccde06d9-35ab-4e89-86e9-97e71250f0ee)

Select "Raspberry Pi OS Lite (32-bit) - A port of Debian Bookworm with no desktop environment" (on my screenshot it says the image is cached on my computer, but the website states the file as being 508MB)

![Screenshot 2025-01-29 215039](https://github.com/user-attachments/assets/f05cf9f4-a379-46c7-860c-02dd7cab978c)
![Screenshot 2025-01-29 215347](https://github.com/user-attachments/assets/6e0add5b-9934-47a2-b159-9169e2208c65)

Select the correct storage device you want to use in the Raspberry Pi (256gb Samsung microSD for me), then click "Next" (I did not have a picture of the Samsung microSD populated on the Imager screen, so I used a 1GB extra microSD I had lying around.)

![Screenshot 2025-01-29 215729](https://github.com/user-attachments/assets/5c6f041e-b4ac-4345-8e63-0191e85a2260)

A pop-up will appear asking if you would like to use OS customization, select "Edit settings"

![Screenshot 2025-01-29 215757](https://github.com/user-attachments/assets/a996be5b-fbaa-4c54-9c83-cc8b031de327)

Under the "General" tab, I entered a hostname (demopi1.local) which translates to the manual IP address assigned to the Pi. 
The username (demouser1) and password I entered will allow me to use Windows Powershell to SSH into the headless Pi device.
I also changed the Time Zone to that of one within the TZ my Pi device is located. 
I unchecked the wireless LAN settings because I instead used a PoE/Eth/USB Hub Hat to power and give me a wired LAN connection with one ethernet cable, but you will need to enter your WiFi SSID user and password to connect your Pi to your network if you do not have a PoE hat. 
I have also previously set it up to be connected with both WiFi and wired LAN, which gave the Pi two separate IP addresses to use for a second/fallback DNS server IP. 

![Screenshot 2025-01-29 215942](https://github.com/user-attachments/assets/20ebbad4-ae23-4a70-9d34-828139ffe570)

Next, under the "Services" tab, you are given the option to use the username/password combo entered under the "General" tab in the previous step, or to use a public-key for authorization. 
If using the public-key option, you'll need to click the "RUN SSH-KEYGEN" button which will generate a key pertaining to the device you are using to install the OS using Image. In my case, this would be my Windows 11 mini PC.

![Screenshot 2025-01-29 220002](https://github.com/user-attachments/assets/c29551f9-b823-4e10-a86a-ef4273ece491)

After clicking the "Save" button at the bottom of the window, I then clicked "Yes" to apply the OS customization settings.
Then a window appeared informing me that all data will be erased from the microSD card to overwrite with the Pi OS Lite image selected. I clicked "Yes" to ensure I want to continue. 
I then received an error stating that the install required at least 2.6GB of space, so I was unable to write my demo OS to the 1GB microSD card.
However, having done this previously, the process takes several minutes to complete. Once complete I remove the microSD card from the reader, insert it into the Pi Zero 2 W, and connect the Cat6 cable which powers on the device.

After taking a moment to boot up, if you haven't already manually assigned the IP address for the Pi within your router, now would be a good time to do so. I have the best luck changing IP addresses by setting the manual IP address to the desired address within the router's (ASUS RT-AX86U_Pro) LAN > DHCP settings, then unplugging the Pi device, rebooting the router, then reconnecting the Pi device and it should have set the IP to what you set.

After allowing the Pi to boot up using the customized OS, I opened Windows Powershell and entered:
```bash 
ssh demouser1@demopi1.local
```
#### **Install Docker & Docker Compose**  
```bash
sudo apt update && sudo apt upgrade -y
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
