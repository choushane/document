# 私有Docker Hub 架設方式

  ### 1. 容器安裝 
  ```
     docker pull registry  
  ```
  ### 2. 建立映像檔目錄
  ```
     mkdir /home/docker_registry
  ```   
  ### 3. 產生Local sign 
     *進入 /etc/ssl/openssl.cnf 底下的 [v3_req] 新增 subjectAltName=IP:{Registry_IP} 後再產生Key
       （ 解決'x509: cannot validate certificate for {IP} because it doesn't contain any IP SANs'問題 ）
  ```      
     mkdir /home/docker_registry/certs
     cd /home/docker_registry
     openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt   
  ```  
  ### 4. 啟動 Container
  ```  
     cd /home/docker_registry
     docker run -d -p 5000:5000 --restart=always --name registry -v `pwd`:/var/lib/registry -v `pwd`/certs:/certs \
     -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key registry
  ```     
## 映像檔：
  ## 上傳
    1. 登入
```   
    docker login 192.168.0.1:5000
```   
    2. 使用docker tag 將映像檔標記為 {IP}：{PORT}/{Container Name}，
     （格式為 docker tag IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]）。
```       
        docker tag ba58 192.168.0.1:5000/test
```       
    3. 上傳
```   
      sudo docker push 192.168.0.1:5000/test
```      
   ## 下載
    1. 登入
```   
    docker login 192.168.0.1:5000
```   
    2. 下載 
```   
     sudo docker pull 192.168.0.1:5000/test
```    
   ## 搜索映像檔
```   
     curl -k http://192.168.0.1:5000/v1/search
```     
   ## *Remote 初次操作前需設定：
    1. 建立目錄
```     
     mkdir -p /etc/docker/certs.d/{Registry_IP}:{Registry_Port}/
```     
    2. 把Registry 上產生的 domain.key，domain.crt 放入上述位置 
        *(Ubuntu 16.04 請把 domain.crt 更名為 domain.cert)
    
# 另外還有其他安裝方式：

  ## 套件庫:
    Ubuntu
```    
      sudo apt-get install -y build-essential python-dev libevent-dev python-pip liblzma-dev swig
      sudo pip install docker-registry
```    
    CentOS
```    
      sudo yum install -y python-devel libevent-devel python-pip gcc xz-devel
      sudo python-pip install docker-registry
```      
  ## Source Code:
```  
    sudo apt-get install build-essential python-dev libevent-dev python-pip libssl-dev liblzma-dev libffi-dev
    git clone https://github.com/docker/docker-registry.git
    cd docker-registry
    sudo python setup.py install
```    
    
     然後修改設定檔案，主要修改 dev 模板段的 storage_path 到本地的儲存倉庫的路徑
```     
     cp config/config_sample.yml config/config.yml
```     

  ## 啟動服務
```  
    sudo gunicorn -c contrib/gunicorn.py docker_registry.wsgi:application
```   
    OR
```   
    sudo gunicorn --access-logfile - --error-logfile - -k gevent -b 0.0.0.0:5000 -w 4 --max-requests 100 docker_registry.wsgi:application
```    
