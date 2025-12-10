# README
## Objective
Control robot with DNN
### Procedure 
1. Detect object(karaage or mashmelo)
2. Get pose of object
3. Send pose to robot
4. control robot to grasp 

## Prepare the PC for research
1. install ubuntu 20.04 LTS on new PC.
2. update and upgrade
3. install ROS-noetic
4. install [GPU softwares](https://www.cyberciti.biz/faq/ubuntu-linux-install-nvidia-driver-latest-proprietary-driver/)
5. install miniconda3
6. The version of gazebo is 11.11.0
7. install [ros_control](http://wiki.ros.org/ros_control)
8. install [ros_noetic_moveit](https://moveit.ros.org/install/)
9. make workspace contains utra_ros and camera_ws
10. In utra_ros, obtain [utra_ros packages ](https://github.com/UmbraTek/ut_arm_ros)
11. Install [Realsense SDK and Libraries](https://github.com/IntelRealSense/librealsense/blob/master/doc/distribution_linux.md)

### PC Info 
|Info||
|-|-|
|User|hayashi|
|Password|hayashi|
|IP address|192.168.11.2|

## how to build multiple ROS1 workspace
steps
1. create main folder to keep all the workspace e.g. worksp folder
```
mkdir worksp
```

2. head inside the worksp folder
```
cd worksp
```

3. create the first ros workspace and its src folder e.g. named robot_ws
```
mkdir -p robot_ws/src
```

4. git, make or create the ros package as you want
5. build the ros workspace

6. add the source to the .bashrc and source it
```
echo "source ~/worksp/robot_ws/devel/setup.bash" >> ~/.bashrc
```

7. to build another ros workspace alongside with the first worksp, repeat from the step 3
but when creating the second ros workspace, create in the worksp folder

## August 28th - Run Rviz and connect with arm(Moveit and ros_industrial)
1.  ```
    roslaunch arm_controller utarm_api_server.launch arm_ip:=192.168.11.160
    ```

2.  ```
    roslaunch utra6_850_moveit_config run_with_utra6_850.launch 
    ```

3.  ```
    rosservice call /utsrv/connect "192.168.11.160" 
    ```
4. (Essensial!) You must open "Panels" ->"utra_rviz" -> "utra_panel" and check "Enable" and push "resume"
![Screenshot from 2023-09-01 16-29-13](https://github.com/YoshitakaSAKATA/MyResearchDocument/assets/118269935/07063aef-3b52-45f4-b7ba-9b9b808a34cd)
[The video of robot running](https://mailkyutechjp-my.sharepoint.com/personal/sakata_yoshitaka414_mail_kyutech_jp/_layouts/15/stream.aspx?id=%2Fpersonal%2Fsakata%5Fyoshitaka414%5Fmail%5Fkyutech%5Fjp%2FDocuments%2FHayashiLab%2FProgress%2FIMG%5F4282%2EMOV&referrer=OfficeHome%2EWeb&referrerScenario=RecentVideo%2EView)

## September 1st - Install and update Realsense package
1. Register the server's public key
   ```
   sudo mkdir -p /etc/apt/keyrings
   curl -sSf https://librealsense.intel.com/Debian/librealsense.pgp | sudo tee /etc/apt/keyrings/librealsense.pgp > /dev/null
   ```
   Make sure apt HTTPS support is installed
   ```
   sudo apt-get install apt-transport-https
   ```
2. Add the server to the list of repositories
   ```
   echo "deb [signed-by=/etc/apt/keyrings/librealsense.pgp] https://librealsense.intel.com/Debian/apt-repo `lsb_release -cs` main" | \     sudo tee /etc/apt/sources.list.d/librealsense.list
   
   ```
3. Update and Upgrade
   ```
   sudo apt update
   sudo apt upgrade
   ```

4. Install libraries
   ```
   sudo apt-get install librealsense2-dkms
   sudo apt-get install librealsense2-utils
   ```

5. You can connect and run Realsense with below command
   ```
   realsense-viewer
   ```

![Screenshot from 2023-09-01 15-12-07](https://github.com/YoshitakaSAKATA/MyResearchDocument/assets/118269935/1cceff5e-6161-46d1-aa2c-1ae07d36488e)
[The video of robot running](https://mailkyutechjp-my.sharepoint.com/personal/sakata_yoshitaka414_mail_kyutech_jp/_layouts/15/stream.aspx?id=%2Fpersonal%2Fsakata%5Fyoshitaka414%5Fmail%5Fkyutech%5Fjp%2FDocuments%2FHayashiLab%2FProgress%2FIMG%5F4282%2EMOV&referrer=OfficeHome%2EWeb&referrerScenario=RecentVideo%2EView)
### Run via ROS1
1. Move to workspace
   ```
   cd ~/worksp/camera_ws/src
   ```

2. Download realsense-ros from [Here](https://github.com/IntelRealSense/realsense-ros#installation-instructions) (, and [also](https://github.com/IntelRealSense/realsense-ros/tree/ros1-legacy))
   ```
   git clone https://github.com/IntelRealSense/realsense-ros.git
   cd realsense-ros
   git checkout `git tag | sort -V | grep -P "^2.\d+\.\d+" | tail -1`
   cd ../
   ```
   
3. Build 

3. Error message would be desplayed on terminal, so please install ddynamic_reconfigure
   ```
   sudo apt-get install ros-noetic-ddynamic-reconfigure
   sudo apt update
   ```

4. Build again

5. Source
   ```
   echo "source ~/worksp/camera_ws/devel/setup.bash" >> ~/.bashrc
   source ~/.bashrc
   ```
   
6. Run realsense2_camera using below command
   ```
   roslaunch realsense2_camera rs_camera.launch align_depth:=true
   ```

   Run Image View on another terminal
   ```
   rosrun rqt_image_view rqt_image_view
   ```
7. [PointCloud2](https://qiita.com/ReoNagai/items/04dfbcf1f4f3600e8d70)

![Screenshot from 2023-09-01 16-14-14](https://github.com/YoshitakaSAKATA/MyResearchDocument/assets/118269935/2101672c-4005-4e01-a2a4-89b935b260af)

## Test Arducam Multi Camera Adapter Module V2.2 - September 6
Please refer to [Quick start](https://github.com/ArduCAM/RaspberryPi/tree/master/Multi_Camera_Adapter/Multi_Adapter_Board_4Channel)

**Don't get the wrong model when you edit the config.txt!** 
There are 3 cameras, two Camera Module v3(imx708) and one Camera Module v2(imx219). 
When a camera is not recognized, you have to edit sensor number on config.txt.
```
sudo nano /boot/config.txt
```
Please make sure to enter the correct model number.

**This part**
```
dtoverlay="sensor number"
```





## Setup RPi4B - September 8
### RPi Info
   |RPi|Info|
   |----|-------|
   |User|hayashi|
   |Password|hayashi|
   |IP address|192.168.11.3|
   |Port|65530|
1. Test camera module 
2. Setting SSH of RPi4B
   1. enable SSH of RPi
   2. execute below command
      ```
      cd /boot
      sudo mkdir ssh
      ```
      and reboot
      ```
      sudo reboot
      ```
      
   3. Edit sshd_config
      ```
      sudo nano /etc/ssh/sshd_config
      ```
 
      Change this part **from 49512 to 65535 (Don't forget to remove "#"!)**
      ```
      Port 65530
      ```

      and restart ssh
      ```
      sudo /etc/init.d/ssh restart
      ```
    4. PC can connect RPi via SSH with new Port number 
       ```
       ssh hayashi@192.168.11.3 -p 65530
       ```
    5. Make secret key and public key **on PC**
       ```
       ssh-keygen -t rsa -b 4096 -C "sakatayoshitaka1031@gmail.com"
       ```
       You will be asked where to generate the key, so press Enter

    6. Make sure to execute below command **on RPi**
       ```
       mkdir ~/.ssh
       ```
       Send public key **from PC to RPi**
       ```
       cd ~/.ssh
       scp -P 65530 id_rsa.pub hayashi@192.168.11.3:/home/hayashi/.ssh
       ```

       Add id_rsa.pub to authorized_keys **on RPi**
       ```
       cd ~/.ssh/
       cat id_rsa.pub >> authorized_keys
       chmod 600 authorized_keys
       chmod 700 ~/.ssh
       rm ~/.ssh/id_rsa.pub
       ```

    7. Connect with below command
       ```
       ssh hayashi@192.168.11.3 -i hayashi -p 65530
       ```

    8. Change SSH configuration on RPi
       ```
       sudo nano /etc/ssh /sshd_config
       ```

       Change this part 
       ``` conf
       #PermitRootLogin prohibit-password #to "PermitRootLogin no"
       #PasswordAuthentication yes #to "PasswordAuthentication no"
       #PermitEmptyPasswords no #remove "#"
       ```

    9. Make config file **with VScode on PC**
       ```
       Host hayashi
               HostName 192.168.11.3
               User hayashi
               Port 65530
               IdentityFile hayashi
       ```

    10. You can connect RPi with VScode
    
## Send stream to PC - September 11
Please refer to [this document about Picamera2](https://datasheets.raspberrypi.com/camera/picamera2-manual.pdf)

## Send stream to PC with ROS1 - September 26
1. ```
   roscore
   ```
2. On new terminal, go to camera_ws and run program to recieve and publish Image
   ```
   cd ~/worksp/camera_ws/
   rosrun gripper_camera TcpStreamAndPublish.py
   ```
   
3. Connect RPi on SSH and run program to capture and send image to PC with TCP
   ```
   python TcpStreamClient.py
   ```
4. You can see Image on rqt_image
   Images will be stored in **camera_ws/src** as image_YMDHMS.jpg
   ```
   rqt_image_view
   ```
   
## Control gripper 
### Imformation about motor
|Part|Name|
|-|-|
|Stepper Motor|Makerbase mks servo42c v1.0(Nema17)|




## MoveItによるアームのコントロール
1. 以下のコマンドを実行
    ```
    roslaunch arm_controller utarm_api_server.launch arm_ip:=192.168.11.160
    roslaunch utra6_850_moveit_config run_with_utra6_850.launch
    ```

2. 新しくモーションプランニング用のパッケージを作り, 実行 
    /home/hayashi/worksp/utra_ws/src/utra_motion_planning/src/utra_motion_test.pyにある
   ```
   rosrun utra_motion_planning utra_motion_test.py
   ```

   **怪しい動きをしたらすぐに緊急停止ボタンを押す**

## YOLACT環境構築
1. リポジトリインストール
   ```
   $ git clone https://github.com/dbolya/yolact.git
   $ cd yolact
   ```
2. condaで仮想環境(yolact-env)をつくる
   ```
   $ conda env create -f environment.yml
   ```
3. 環境起動
   ```
   $ conda activate yolact-env
   ```

## Yolov7_ros環境構築
上のYOLACTの環境構築で詰まったのでYOLOv7を使う
[Yolov7_ros](https://github.com/lukazso/yolov7-ros)
1. ワークスペース(yolo_ws)を作る
   ```
   $ cd worksp
   $ mkdir -p yolo_ws/src
   ```
2. vision_msgとcommon_msgをワークスペースに入れておく(noetic-develで)
   ```
   $ sudo apt-get install ros-noetic-vision-msgs
   ```

   ```
   $ git clone -b noetic-devel https://github.com/ros/common_msgs.git
   $ git clone -b noetic-devel https://github.com/ros-perception/vision_msgs.git
   ```

3. yolov7.launchを編集する<br>
   weights_path, img_topic, param_nameのところ
   ```xml
   <launch>
        <node pkg="yolov7_ros" type="detect_ros.py" name="detect" output="screen"
        ns="yolov7">
            <!-- Download the official weights from the original repo -->
            <param name="weights_path" type="str"
            value="/home/hayashi/worksp/yolo_ws/src/yolov7_ros/weights/yolov7-e6e.pt"/>
            <!-- Path to a class_labels.txt file containing your desired class labels. The i-th entry corresponds to the i-th class id. For example, in coco class label 0 corresponds to             'person'. Files for the coco and berkeley deep drive datasets are provided in the 'class_labels/' directory. If you leave it empty then no class labels are visualized.-->
            <param name="classes_path" type="str" value="/home/hayashi/worksp/yolo_ws/src/yolov7_ros/class_labels/coco.txt" />
            <!-- topic name to subscribe to -->
            <param name="img_topic" type="str" value="/camera/color/image_raw" />
            <!-- topic name for the detection output -->
            <param name="out_topic" type="str" value="yolov7" />
            <!-- confidence threshold -->
            <param name="conf_thresh" type="double" value="0.35" />
            <!-- intersection over union threshold -->
            <param name="iou_thresh" type="double" value="0.45" />
            <!-- queue size for publishing -->
            <param name="queue_size" type="int" value="1" />
            <!-- image size to which to resize each input image before feeding into the
            network (the final output is rescaled to the original image size) -->
            <param name="img_size" type="int" value="640" />
            <!-- flag whether to also publish image with the visualized detections -->
            <param name="visualize" type="bool" value="true" />
            <!-- 'cuda' or 'cpu' -->
            <param name="device" type="str" value="cuda" />
        </node>
    </launch>
   ```
4. 忘れずに(動かなかったときも)
   ```
   $ source devel/setup.bash
   ```
   
5. 起動方法
   
   realsenseノードを起動した後
   ```
   $ roslaunch yolov7_ros yolov7.launch
   ```
6. エラーが起きたときの参考用
   1. [Error when invoking catkin_make #26](https://github.com/lukazso/yolov7-ros/issues/26)
   2. [Issues and resolution when installing yolov7_ros #14](https://github.com/lukazso/yolov7-ros/issues/14)

## データセット作成
**今後アノテーションツールを変えるかも->cvat**
1. labelmeの仮想環境を作る
2. データセット作成(今回のラベルはmarshmallow)
   webから集めた20枚の画像と自分で撮った80枚の画像の計100枚の画像に対してアノテーション
3. [labelme2yolo](https://github.com/rooneysh/Labelme2YOLO/blob/main/labelme2yolo.py)を使ってjsonをtxtにコンバート
4. 

## 学習
バッチ数8(これが限界)、エポック数300で学習
```
$ python3 train.py --workers 8 --device 0 --batch-size 8 --data data/YOLODataset/dataset.yaml  --img 640 640 --cfg cfg/training/yolov7.yaml --weights 'yolov7_training.pt' --name yolov7-marshmallow --hyp data/hyp.scratch.custom.yaml
```

## 実行
launchファイルを以下のように編集<br>
デフォルトのconf(confidence)だと誤検知が多かったため0.6付近で設定
``` xml
    <launch>
        <node pkg="yolov7_ros" type="detect_ros.py" name="detect" output="screen"
        ns="yolov7">
            <!-- Download the official weights from the original repo -->
            <param name="weights_path" type="str"
            value="/home/hayashi/worksp/yolo_ws/src/yolov7_ros/src/marshmallow_8_300_1128_best.pt"/>
            <!-- Path to a class_labels.txt file containing your desired class labels. The i-th entry corresponds to the i-th class id. For example, in              coco class label 0 corresponds to 'person'. Files for the coco and berkeley deep drive datasets are provided in the 'class_labels/'                      directory. If you leave it empty then no class labels are visualized.-->
            <param name="classes_path" type="str" value="/home/hayashi/worksp/yolo_ws/src/yolov7_ros/class_labels/coco_marshmallow.txt" />
            <!-- topic name to subscribe to -->
            <param name="img_topic" type="str" value="/camera/color/image_raw" />
            <!-- topic name for the detection output -->
            <param name="out_topic" type="str" value="yolov7" />
            <!-- confidence threshold -->
            <param name="conf_thresh" type="double" value="0.65" />
            <!-- intersection over union threshold -->
            <param name="iou_thresh" type="double" value="0.45" />
            <!-- queue size for publishing -->
            <param name="queue_size" type="int" value="1" />
            <!-- image size to which to resize each input image before feeding into the
            network (the final output is rescaled to the original image size) -->
            <param name="img_size" type="int" value="640" />
            <!-- flag whether to also publish image with the visualized detections -->
            <param name="visualize" type="bool" value="true" />
            <!-- 'cuda' or 'cpu' -->
            <param name="device" type="str" value="cuda" />
        </node>
    </launch>
```
## 検出した座標をtfで変換
yolov7_rosで検出された物体の中心座標はdetection_convertのdetection_listener.pyでPointStamped形式でPublishされる. 
tf関係でエラーが出たため
```
$ sudo apt install ros-noetic-geometry2
```
でtf2_toolsをインストールして確認

tfがうまく行ってない
link6->camera_link->color, depth, etc...にしたい</br>
URDFにrealsenseのURDFを統合したらうまく行った


## serialUSBのマッピング
1. FT232R
    |ID|Value|
    |-|-|
    |manufacturer|FTDI|
    |idVender|0403|
    |idProduct|6001|
    |serial|B00054OO|
    |product|FT232R USB UART|

2. venderID, productID, serial情報の取得
    ```
    $ udevadm info -a -p $(udevadm info -q path -n /dev/ttyUSB0)
    ```
    or
    ```
    $ dmesg
    ```
3. udevルールファイルの作成
   1. nanoで編集
       ```
       $ sudo nano /etc/udev/rules.d/10-usb-serial.rules
       ```
   2. 編集内容
       ```
       KERNEL=="ttyUSB*", ATTRS{idVendor}=="0403", ATTRS{idProduct}="6001", ATTRS{serial}=="XXXXXXXX",     SYMLINK+="ttyUSB_Gripper"
       ```
   3. reload
      ```
      $ sudo service udev reload
      ```
   4. 接続後
      ```
      $ ls -l /dev/ttyUSB*
      ```
   5. 権限付与
      ```
      $ sudo chmod 666 /dev/ttyUSB_Gripper
      ```

## January-9th データ取得用にキー入力の間画像を保存するように変更 

## 深層学習
1. 参考
    1. [kerasで2入力1出力のCNN設計をしたい](https://ja.stackoverflow.com/questions/72004/keras%E3%81%A72%E5%85%A5%E5%8A%9B1%E5%87%BA%E5%8A%9B%E3%81%AEcnn%E8%A8%AD%E8%A8%88%E3%82%92%E3%81%97%E3%81%9F%E3%81%84)
    2. [KerasでVGG16を使った転移学習/ファインチューニングをしてみた](https://qiita.com/02130515/items/0c6d0e5d03332acc665e)
    3. [ニューラルネットワークを使った画像分類（VGG16）の基本的な手順を解説する](https://qiita.com/ykoji/items/e9c2c5d7288c6290d21b)
2. 深層学習の構想
    1. 入力: グリッパーの内部からの画像<br>
    2. ラベル
        1. 把持しているか->grasped(1,0)
        2. 把持対象が変形しているか->Deformed(1,0)
        3. x方向の位置->(0,1,2)
        4. y方向の位置->(0,1,2)
    3. VGG16で特徴抽出->4ラベル分のFCを作る(位置に関してはsoftmax, Deformationとgraspingに関してはsigmoidで活性化)->4ラベル分の情報を出力
   
3. 仮想環境を作る
   ```
   conda create -n keras_vgg16 python=3.8.10

