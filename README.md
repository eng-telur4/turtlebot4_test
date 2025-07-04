# turtlebot4_test

## やったこと

- Ubuntu専用PCの用意
- Ubuntu 24.04のイメージを公式サイトからダウンロードし、RufusなどのツールでUSBのインストールメディアを作成
- BIOS画面でブートオーダをUSB Memoryに変更
- PCにインストールメディアのUSBを差し込み、インストール開始
- インストールでは基本の表示言語は英語、キーボードはJapaneseを選択
- インストール後ログインしたらターミナルを開き、```sudo apt update -y && sudo apt upgrade -y```を実行
- 日本語入力を有効化する
  - ```sudo apt install ibus-mozc -y```を実行し、その後```sudo reboot```で再起動する
  - 再起動して設定から言語の設定に移動すると、自動で入力がJapanese (Mozc)になっているはず[参考サイト](https://qiita.com/takuya66520126/items/8bb760bf99c4e25364e3 "ubuntuで日本語入力に変更する方法 #Linux - Qiita")
- Turtlebot4の開発環境をセットアップする（以下を参照）

- Turtlebot4に入ってるROS2のバージョン確認

```sh
ubuntu@turtlebot4:~$ echo $ROS_DISTRO
jazzy
```

- LIFEBOOKのWindows 11のWSLにUbuntu 24.04をいれ、ROS2 Jazzyをインストールする

```sh
sudo apt update -y && sudo apt upgrade -y
locale  # check for UTF-8
sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
locale  # verify settings
sudo apt install software-properties-common
sudo add-apt-repository universe
sudo apt update && sudo apt install curl -y
export ROS_APT_SOURCE_VERSION=$(curl -s https://api.github.com/repos/ros-infrastructure/ros-apt-source/releases/latest | grep -F "tag_name" | awk -F\" '{print $4}')
curl -L -o /tmp/ros2-apt-source.deb "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.$(. /etc/os-release && echo $VERSION_CODENAME)_all.deb" # If using Ubuntu derivates use $UBUNTU_CODENAME
sudo apt install /tmp/ros2-apt-source.deb
sudo apt update && sudo apt install ros-dev-tools
sudo apt update
sudo apt upgrade
sudo apt install ros-jazzy-desktop
sudo apt install ros-jazzy-ros-base
source /opt/ros/jazzy/setup.bash
``` 

- WSLのUbuntu 24.0にTurtlebo4のJazzy版を入れる

```sh
sudo apt update && sudo apt install ros-jazzy-turtlebot4-desktop
```

- Turtlebot4のラズパイにリモート接続

```sh
ssh ubuntu@192.168.1.153
```

- 一旦Windows 11にWireless Controllerを接続し、PowerShell（管理者権限）でWireless ControllerのMACアドレスを確認

```sh
PS C:\WINDOWS\system32> Get-PnpDevice -Class Bluetooth | Select-Object FriendlyName, InstanceId, Status                                                                                                                                         FriendlyName                           InstanceId                                                                       ------------                           ----------                                                                       Microsoft Bluetooth LE Enumerator      BTH\MS_BTHLE\6&1825021F&0&3                                                      soundcore P40i                         BTHENUM\DEV_880E851DF20B\7&A7FCA4F&0&BLUETOOTHDEVICE_880E851DF20B                soundcore P40i Avrcp Transport         BTHENUM\{0000110E-0000-1000-8000-00805F9B34FB}_LOCALMFG&0002\7&A7FCA4F&0&880E... Device Identification Service          BTHENUM\{00001200-0000-1000-8000-00805F9B34FB}_VID&0002054C_PID&05C4\7&A7FCA4... soundcore P40i Avrcp Transport         BTHENUM\{0000110C-0000-1000-8000-00805F9B34FB}_LOCALMFG&0002\7&A7FCA4F&0&880E... Microsoft Bluetooth Enumerator         BTH\MS_BTHBRB\6&1825021F&0&1                                                     Intel(R) Wireless Bluetooth(R)         USB\VID_8087&PID_0026\5&1730C901&0&10                                            Bluetooth Device (RFCOMM Protocol TDI) BTH\MS_RFCOMM\6&1825021F&0&0                                                     Wireless Controller                    BTHENUM\DEV_A05A5D7E5C6A\7&A7FCA4F&0&BLUETOOTHDEVICE_A05A5D7E5C6A  
```

- Turtlebot4のラズパイからWireless Controllerに接続

```sh
ubuntu@turtlebot4:~$ sudo bluetoothctl
[sudo] password for ubuntu:
Waiting to connect to bluetoothd...[bluetooth]# hci0 new_settings: powered bondable ssp br/edr le secure-conn
[bluetooth]# Agent registered
[bluetooth]# [CHG] Controller D8:3A:DD:36:3A:CB Pairable: yes
[bluetooth]# scan on
[bluetooth]# hci0 type 7 discovering on
[bluetooth]# SetDiscoveryFilter success
[bluetooth]# Discovery started
[bluetooth]# [CHG] Controller D8:3A:DD:36:3A:CB Discovering: yes
[bluetooth]# [NEW] Device 59:D3:04:C4:4B:0F 59-D3-04-C4-4B-0F
[bluetooth]# [NEW] Device C8:FF:77:B8:E1:95 VR9-JP-KBA0811A
[bluetooth]# [NEW] Device 4F:29:D4:EF:A5:91 Quest 3S
[bluetooth]# [NEW] Device 6F:6D:95:A3:72:A2 6F-6D-95-A3-72-A2
[bluetooth]# [NEW] Device 54:2F:04:37:CC:8E 54-2F-04-37-CC-8E
[bluetooth]# [NEW] Device 5D:81:6A:DB:FB:D1 5D-81-6A-DB-FB-D1
[bluetooth]# [NEW] Device C8:FF:77:88:87:DE E4F-JP-MFA1534A
[bluetooth]# [NEW] Device 70:26:05:D3:25:D4 Q-SL2
[bluetooth]# [NEW] Device 70:26:05:D5:B9:E3 Q-SL2
[bluetooth]# [NEW] Device 15:5F:95:8E:EF:D1 15-5F-95-8E-EF-D1
[bluetooth]# [NEW] Device 74:25:67:E4:C7:13 74-25-67-E4-C7-13
[bluetooth]# [NEW] Device 42:80:05:8F:A2:BF 42-80-05-8F-A2-BF
[bluetooth]# [NEW] Device 57:CB:DA:7A:E6:FA 57-CB-DA-7A-E6-FA
[bluetooth]# [NEW] Device 4D:7B:EE:77:51:E6 4D-7B-EE-77-51-E6
[bluetooth]# [NEW] Device 5B:60:65:49:19:4B Quest 3
[bluetooth]# [NEW] Device 8C:52:19:AC:03:BB 8C-52-19-AC-03-BB
[bluetooth]# [NEW] Device 4A:7B:E4:8D:D1:4D 4A-7B-E4-8D-D1-4D
[bluetooth]# [NEW] Device 09:0E:85:1D:F2:0C 09-0E-85-1D-F2-0C
[bluetooth]# [NEW] Device C8:FF:77:CC:F1:3D VR9-JP-MBA1471A
[bluetooth]# [NEW] Device 77:48:41:FC:03:69 77-48-41-FC-03-69
[bluetooth]# [NEW] Device 61:A0:3A:E3:30:82 Quest 3
[bluetooth]# [NEW] Device 79:96:2F:89:A7:A4 79-96-2F-89-A7-A4
[bluetooth]# [NEW] Device 6D:44:37:8E:E9:FA 6D-44-37-8E-E9-FA
[bluetooth]# [NEW] Device 45:7B:F8:3F:AB:33 45-7B-F8-3F-AB-33
[bluetooth]# [NEW] Device 74:78:17:08:58:5C 74-78-17-08-58-5C
[bluetooth]# [NEW] Device FF:0F:00:0F:68:31 FF-0F-00-0F-68-31
[bluetooth]# [CHG] Device 59:D3:04:C4:4B:0F RSSI: 0xffffffdd (-35)
[bluetooth]# [NEW] Device 55:16:17:90:3C:22 55-16-17-90-3C-22
[bluetooth]# [CHG] Device 61:A0:3A:E3:30:82 RSSI: 0xffffffc6 (-58)
[bluetooth]# [NEW] Device 90:17:C8:A9:27:5F SPWN_H37_A9275D-BT
[bluetooth]# [NEW] Device FF:0F:00:11:FC:40 FF-0F-00-11-FC-40
[bluetooth]# [CHG] Device 55:16:17:90:3C:22 RSSI: 0xffffffd8 (-40)
[bluetooth]# [CHG] Device C8:FF:77:CC:F1:3D RSSI: 0xffffffb8 (-72)
[bluetooth]# [CHG] Device 70:26:05:D3:25:D4 RSSI: 0xffffffbe (-66)
[bluetooth]# [CHG] Device 70:26:05:D3:25:D4 UUIDs: b88612e9-b3c1-45ee-aaf5-5e145e2d9831
[bluetooth]# [CHG] Device 59:D3:04:C4:4B:0F RSSI: 0xffffffd0 (-48)
[bluetooth]# [NEW] Device A0:5A:5D:7E:5C:6A Wireless Controller
[bluetooth]# [CHG] Device A0:5A:5D:7E:5C:6A Modalias: usb:v054Cp09CCd0100
[bluetooth]# [CHG] Device A0:5A:5D:7E:5C:6A UUIDs: 00001124-0000-1000-8000-00805f9b34fb
[bluetooth]# [CHG] Device A0:5A:5D:7E:5C:6A UUIDs: 00001200-0000-1000-8000-00805f9b34fb
hci0 type 7 discovering off
[bluetA0:5A:5D:7E:5C:6AA:5D:7E:5C:6A
[bluetooth]# [CHG] Device A0:5A:5D:7E:5C:6A Trusted: yes
[bluetooth]# Changing A0:5A:5D:7E:5C:6A trust succeeded
[blueA0:5A:5D:7E:5C:6AA:5D:7E:5C:6A
Attempting to pair with A0:5A:5D:7E:5C:6A
[bluetooth]# hci0 device_flags_changed: A0:5A:5D:7E:5C:6A (BR/EDR)
[bluetooth]#      supp: 0x00000001  curr: 0x00000000
[bluetooth]# hci0 A0:5A:5D:7E:5C:6A type BR/EDR connected eir_len 26
[CHG] Device A0:5A:5D:7E:5C:6A Connected: yes
[Wireless Controller]# hci0 new_link_key A0:5A:5D:7E:5C:6A type 0x04 pin_len 0 store_hint 1
[Wireless Controller]# [CHG] Device A0:5A:5D:7E:5C:6A Bonded: yes
[Wireless Controller]# hci0 device_flags_changed: A0:5A:5D:7E:5C:6A (BR/EDR)
[Wireless Controller]#      supp: 0x00000001  curr: 0x00000001
[Wireless Controller]# [CHG] Device A0:5A:5D:7E:5C:6A WakeAllowed: yes
[CHG] Device A0:5A:5D:7E:5C:6A Modalias: usb:v054Cp05C4d0100
[CHG] Device A0:5A:5D:7E:5C:6A ServicesResolved: yes
[CHG] Device A0:5A:5D:7E:5C:6A Paired: yes
Pairing successful
[WirelesA0:5A:5D:7E:5C:6Annect A0:5A:5D:7E:5C:6A
Attempting to connect to A0:5A:5D:7E:5C:6A
[Wireless Controller]# Connection successful
[Wireless Controller]# hci0 type 7 discovering on
[Wireless Controller]# [CHG] Device C8:FF:77:88:87:DE RSSI: 0xffffffc9 (-55)
[Wireless Controller]# [CHG] Device 4A:7B:E4:8D:D1:4D RSSI: 0xffffffc1 (-63)
[Wireless Controller]# [CHG] Device 6D:44:37:8E:E9:FA RSSI: 0xffffffb6 (-74)
[Wireless Controller]# [CHG] Device 59:D3:04:C4:4B:0F RSSI: 0xffffffd8 (-40)
[Wireless Controller]# [NEW] Device 8C:52:19:78:0D:C4 MiRZA_4451
[Wireless Controller]# [CHG] Device 79:96:2F:89:A7:A4 RSSI: 0xffffffb8 (-72)
[Wireless Controller]# [CHG] Device 79:96:2F:89:A7:A4 RSSI: 0xffffffc0 (-64)
[Wireless Controller]# [CHG] Device C8:FF:77:CC:F1:3D RSSI: 0xffffffc2 (-62)
[Wireless Controller]# [CHG] Device 4A:7B:E4:8D:D1:4D RSSI: 0xffffffc9 (-55)
[Wireless Controller]# hci0 type 7 discovering off
[Wireless Controller]# [DEL] Device 70:26:05:D5:B9:E3 Q-SL2
[Wireless Controller]# exit
```

- Turtlebot4のコモンパッケージをインストール
  - turtlebot4-description：ロボット尾URDF記述と各コンポーネントのメッシュファイル
  - turtlebot4-msgs：Turtlebot4で使用されるカスタムメッセージ
  - turtlebot4-navigation：SLAMとナビゲーションを使用するための起動・構成ファイル・ナビゲータ用Pythonノード
  - turtlebot4-node：ロボットのHMIとその他のロジックを制御するrclcppノードのソースコード

```sh
sudo apt update
sudo apt install ros-jazzy-turtlebot4-description \
ros-jazzy-turtlebot4-msgs \
ros-jazzy-turtlebot4-navigation \
ros-jazzy-turtlebot4-node
```

- bringupをインストール

```sh
sudo apt install ros-jazzy-turtlebot4-bringup
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
