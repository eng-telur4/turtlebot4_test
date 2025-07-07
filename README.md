# turtlebot4_test

## やったこと

### 環境構築

1. WSLで行う場合

- コントロールパネルでWindowsのWSLを有効化する
- Microsoft StoreからUbuntu 24.04をインストールする
- 起動時にユーザ名とパスワードを入力し、とりあえず```sudo apt update -y && sudo apt upgrade -y```を実行する
- Turtlebot4の開発環境をセットアップする（以下を参照）

2. Ubuntu専用PCを使う場合

- Ubuntu専用PCの用意
- Ubuntu 24.04のイメージを公式サイトからダウンロードし、RufusなどのツールでUSBのインストールメディアを作成
- BIOS画面でブートオーダをUSB Memoryに変更
- PCにインストールメディアのUSBを差し込み、インストール開始
- インストールでは基本の表示言語は英語、キーボードはJapaneseを選択
- インストール後ログインしたらターミナルを開き、```sudo apt update -y && sudo apt upgrade -y```を実行
- 日本語入力を有効化する
  - ```sudo apt install ibus-mozc mozc-utils-gui -y```を実行し、その後```sudo reboot```で再起動する（mozc-utils-guiが無いと設定が開かない、またキーボード設定では一番上をJapanese(Mozc)にし、二番目をJapaneseにする。二番目を消してしまうと、記号などの配置がEnglishと同じものになり使いづらい）
  - 再起動して設定から言語の設定に移動すると、自動で入力がJapanese (Mozc)になっているはず[参考サイト](https://qiita.com/takuya66520126/items/8bb760bf99c4e25364e3 "ubuntuで日本語入力に変更する方法 #Linux - Qiita")
- Turtlebot4の開発環境をセットアップする（以下を参照）

### Turtlebot4の開発環境のセットアップ

- Turtlebot4のラズパイにリモート接続（Turtlebot4とPCは同じネットワークに接続されている必要がある。また5GHz帯でないといけない）

```sh
ssh ubuntu@<Turtlebot4のIPアドレス>
```

- Turtlebot4に入ってるROS2のバージョン確認（Turtlebot4とPCで同じバージョンを使用する必要がある）

```sh
ubuntu@turtlebot4:~$ echo $ROS_DISTRO
jazzy
```

- UbuntuにROS2 Jazzyをインストールする

```sh
locale  # check for UTF-8
sudo apt install locales -y
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
locale  # verify settings
sudo apt install software-properties-common -y
sudo add-apt-repository universe
sudo apt install curl -y
export ROS_APT_SOURCE_VERSION=$(curl -s https://api.github.com/repos/ros-infrastructure/ros-apt-source/releases/latest | grep -F "tag_name" | awk -F\" '{print $4}')
curl -L -o /tmp/ros2-apt-source.deb "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.$(. /etc/os-release && echo $VERSION_CODENAME)_all.deb" # If using Ubuntu derivates use $UBUNTU_CODENAME
sudo apt install /tmp/ros2-apt-source.deb -y
sudo apt install ros-dev-tools ros-jazzy-desktop ros-jazzy-ros-base ros-jazzy-turtlebot4-desktop ros-jazzy-turtlebot4-description ros-jazzy-turtlebot4-msgs ros-jazzy-turtlebot4-navigation ros-jazzy-turtlebot4-node ros-jazzy-turtlebot4-bringup -y
source /opt/ros/jazzy/setup.bash
```

- UbuntuのターミナルからすぐにROSを操作できるよう、以下の設定を```~/.bashrc```に追記する
- 記述後、ターミナルで```source ~/.bashrc```を実行（もしくは、開いている全てのターミナルを閉じて、開き直す）

```sh
source /opt/ros/jazzy/setup.bash
export ROS_DOMAIN_ID=0
export RMW_IMPLEMENTATION=rmw_fastrtps_cpp
```

- 同じPC内でROSのトピック通信ができるかどうか確認する
  - ターミナルのタブを2つ開き、1つのタブで```ros2 run demo_nodes_cpp talker```を実行し、結果に```... Hello world <数字>```といった感じの出力がされるか確認する
  - もう1つ開いたタブで```ros2 run demo_nodes_cpp listener```を実行し、1つ目のタブで表示されいている内容と同じような文字列が表示されるかどうか確認する
 
- PCとTurtlebot4間で通信できるか確認する(WSLの場合のみ)
  - Windows上でUbuntuを実行している場合は、WSL(Windows Subsystem for Linux)という仮想化システム上で動かすことになるが、この仮想化システムはWindowsとは違う独自の仮想ネットワークを構成してしまうため、WSL上のUbuntuからだと直接外部のTurtlebot4などと通信することが不可能
  - Windowsと同様のネットワークを使用できるようにするため、WSLの設定を書き換える
  - まずWSLを完全にシャットダウンするために、タブは全て消し、コマンドプロンプトを実行して```wsl --shutdown```と実行してPC自体を再起動する
  - ```C:/Users/<ユーザ名>/```の階層に移動し、```.wslconfig```ファイルを作成する（ファイル接頭辞としてドット「.」を入れる：隠しファイルのため）
  - ```.wslconfig```に以下の内容を記述する。記述後はUbuntuを起動する
  - (必要であればUbuntu上で```ss -ta```コマンドなどを実行して、Windowsのネットワークと同様のものを認識できているか確認する)
 
```sh
[wsl2]
# Mirrored Networking Mode を有効にする
networkingMode=mirrored
```
 
- PCとTurtlebot4間で通信できるか確認する(1)
  - 上記と同様にタブを2つ開き、1つのタブで```ros2 run demo_nodes_cpp talker```を実行し、結果に```... Hello world <数字>```といった感じの出力がされるか確認する
  - もう1つ開いたタブでは、SSH接続でTurtlebot4にログインし、そこで```ros2 run demo_nodes_cpp listener```を実行、1つ目のタブで表示されている内容と同じような文字列が表示されるかどうか確認する
 
- PCとTurtlebot4間で通信できるか確認する(2)
  - PCとTurtlebot4で以下のコマンドを実行し、同様の内容が表示されるか確認する
 
```sh
ros2 topic list
ros2 node list
```

- Turtlebot4のラズパイからWireless Controllerに接続
  - ```sudo bluetoothctl```を実行し、```[bluetooth]# ```の画面で```scan on```と入力
  - コントローラの真ん中の丸いボタンと左上の楕円形のボタンを同時に長押しし、白いランプが点滅したら離す
  - 白いランプが点滅したら、ターミナルに```A0:5A:5D:7E:5C:6A```や```Wireless Controller```と文字が表示されるか確認
  - 確認できたら、```trust A0:5A:5D:7E:5C:6A```, ```pair A0:5A:5D:7E:5C:6A```, ```connect A0:5A:5D:7E:5C:6A```と連続で入力する
  - 接続成功するとコントローラが青く光り続ける
  - 接続できなかったら```untrust A0:5A:5D:7E:5C:6A```と```remove A0:5A:5D:7E:5C:6A```と入力した後に```exit```で一旦抜け、また```sudo bluetoothctl```から同じ手順で試す
  - コントローラのMACアドレスがまだ既知ではない場合は、まずWindows 11にWireless Controllerを接続し、PowerShell（管理者権限）で以下のコマンドを実行してWireless ControllerのMACアドレスを確認し、そのMACアドレスを確認してから```sudo bluetoothctl```での設定に戻る（実行結果下部にWireless Controllerが表示されているか確認。その後の文字列にあるA05A5D7E5C6AがMACアドレスになっている）
  - 接続できた時点で、コントローラの```L1```または```R1```ボタンを押しながら左ジョイスティックを動かすとTurtlebot4を動かすことができる

```sh
PS C:\WINDOWS\system32> Get-PnpDevice -Class Bluetooth | Select-Object FriendlyName, InstanceId, Status
FriendlyName                           InstanceId
------------                           ----------
Microsoft Bluetooth LE Enumerator      BTH\MS_BTHLE\6&1825021F&0&3
soundcore P40i                         BTHENUM\DEV_880E851DF20B\7&A7FCA4F&0&BLUETOOTHDEVICE_880E851DF20B
soundcore P40i Avrcp Transport         BTHENUM\{0000110E-0000-1000-8000-00805F9B34FB}_LOCALMFG&0002\7&A7FCA4F&0&880E...
Device Identification Service          BTHENUM\{00001200-0000-1000-8000-00805F9B34FB}_VID&0002054C_PID&05C4\7&A7FCA4...
soundcore P40i Avrcp Transport         BTHENUM\{0000110C-0000-1000-8000-00805F9B34FB}_LOCALMFG&0002\7&A7FCA4F&0&880E...
Microsoft Bluetooth Enumerator         BTH\MS_BTHBRB\6&1825021F&0&1
Intel(R) Wireless Bluetooth(R)         USB\VID_8087&PID_0026\5&1730C901&0&10
Bluetooth Device (RFCOMM Protocol TDI) BTH\MS_RFCOMM\6&1825021F&0&0
Wireless Controller                    BTHENUM\DEV_A05A5D7E5C6A\7&A7FCA4F&0&BLUETOOTHDEVICE_A05A5D7E5C6A  
```

## 参考サイト

- [TurtleBot 4 - Clearpath Robotics](https://clearpathrobotics.com/turtlebot-4/ "TurtleBot 4 - Clearpath Robotics")
- [Basic Setup · User Manual](https://clearpathrobotics.com/turtlebot-get-started/ "Basic Setup · User Manual")
- [[ROS 2 入門]  ROS 2 Humble を使った TurtleBot3 シミュレーション手順 (Ubuntu 22.04.4 LTS) #ROS2 - Qiita](https://qiita.com/Futo_Horio/items/2e78b3d160a0026d180c "[ROS 2 入門]  ROS 2 Humble を使った TurtleBot3 シミュレーション手順 (Ubuntu 22.04.4 LTS) #ROS2 - Qiita")
- [Unable to locate package ros-humble-turtlebot4-desktop  · Issue #168 · turtlebot/turtlebot4](https://github.com/turtlebot/turtlebot4/issues/168 "Unable to locate package ros-humble-turtlebot4-desktop  · Issue #168 · turtlebot/turtlebot4")
- [Ubuntu (deb packages) — ROS 2 Documentation: Humble  documentation](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debs.html "Ubuntu (deb packages) — ROS 2 Documentation: Humble  documentation")
- [Ubuntu (deb packages) — ROS 2 Documentation: Jazzy  documentation](https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html "Ubuntu (deb packages) — ROS 2 Documentation: Jazzy  documentation")
- [Why is Wi-Fi asking for PIN instead of password?](https://help.comporium.com/residential/s/article/Why-is-Wi-Fi-asking-for-PIN-instead-of-password "Why is Wi-Fi asking for PIN instead of password?")
- [Create® 3 Docs](https://iroboteducation.github.io/create3_docs/ "Create® 3 Docs")
- [iRobot® Create® 3 turtlebot 4 | 3D CAD Model Library | GrabCAD](https://grabcad.com/library/irobot-create-3-turtlebot-4-1 "iRobot® Create® 3 turtlebot 4 | 3D CAD Model Library | GrabCAD")
- [Home](http://192.168.1.142:8080/ "Home")
- [Turtlebot4リポジトリ](https://github.com/turtlebot/turtlebot4 "")
