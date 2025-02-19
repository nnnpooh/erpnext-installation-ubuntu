# Script to install Thai Fonts on Ubuntu

- You can change the location of the zip files accordingly. All fonts are google fonts.

```sh
FONT_NAME=Prompt
#FONT_NAME=Sarabun
sudo apt-get install unzip -y &&
mkdir -p ~/fonts-temp &&
wget -P ~/fonts-temp https://github.com/nnnpooh/thai-fonts/raw/refs/heads/main/${FONT_NAME}.zip &&
sudo mkdir -p /usr/share/fonts/googlefonts &&
sudo unzip -o ~/fonts-temp/${FONT_NAME}.zip -d /usr/share/fonts/googlefonts &&
rm -fr ~/fonts-temp &&
sudo fc-cache -fv
```
