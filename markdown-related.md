### 一、安装remarkable
wget https://remarkableapp.github.io/files/remarkable_1.62_all.deb  
sudo apt-get install gdebi-core  
sudo gdebi remarkable_1.62_all.deb  

### 二、HTML转换Markdown 
apt-get install pandoc  
pandoc XXX.html -f html -t markdown_github -s -o XXX.md  
