# Amazon GreenGrass + OpenVINO  環境架設

  ## 環境 
    * Ubuntu 16.04 - x86
    * Docker 17.05.0-ce, build 89658be
  
  ## 準備下列檔案
    [Amazon GreenGrass 1.7.1](https://d1onfpft10uf5o.cloudfront.net/greengrass-core/downloads/1.7.1/greengrass-linux-x86-64-1.7.1.tar.gz)
    [OpenVINO 2018-05-455](http://registrationcenter-download.intel.com/akdlm/irc_nas/15078/l_openvino_toolkit_p_2018.5.455.tgz)
    * supervisor.config
    * sysctl.conf
    * Docker File

  ## 部署
    1. 將上述所需檔案放置同一目錄

    2. Docker build
```
    docker build --build-arg "greengrass_release=greengrass-linux-x86-64-1.7.0.tar.gz" .
```
    3. 進入Container 
       *(因為OpenVINO安裝時，需要手動設置選項，所以無法自動安裝)
```
    docker run -it {ID} /bin/bash
```
    4. 安裝OpenVINO 所需套件
```
    apt-get install -y lsb-release sudo cpio vim lsb-core
```

    5. 安裝OpenVINO
```
    tar xvf l_openvino_toolkit_p_2018.5.455.tgz -C /tmp
    cd /tmp/l_openvino_toolkit_p_2018.5.455
    ./install_cv_sdk_dependencies.sh
    ./install.sh
```
      安裝以下套件
      Options > Component selection
      --------------------------------------------------------------------------------
      Select components you wish to modify. When you have completed your changes,
      select option 1 to continue.
      --------------------------------------------------------------------------------

      1. Accept and continue [ default ]
      2. [x] Inference Engine (already installed)
      3. [x] Model Optimizer (already installed)
      4. [x] OpenCV*
      5. [x] OpenVX*
      6. [ ] Models
      7. [x] Algorithms
      8. [x] Intel(R) Media SDK (already installed)

      Install space required:  770MB

    6. 設定環境變數
```
      source /opt/intel/computer_vision_sdk/bin/setupvars.sh
      cd /opt/intel/computer_vision_sdk/deployment_tools/inference_engine/samples
      ./build_samples.sh
```

    7. 刪除不必要的檔案，減少Image容量
```
    apt-get purge libx11.* libqt.* #移除 ubuntu-x11
    apt-get purge dpkg -l | grep ^rc | awk '{ print $2 }'
    apt-get autoremove
    find / -name *.a
    rm /opt/intel/computer_vision_sdk_2018.5.455/openvx/samples/
    rm -rf /opt/intel/computer_vision_sdk_2018.5.455/deployment_tools/inferen ce_engine/lib/ubuntu_18.04/
    rm -rf /opt/intel/computer_vision_sdk_2018.5.455/deployment_tools/inference_engine/lib/centos_7.4/
    apt-get install libgtk2.0-0
```
