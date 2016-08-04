


# 长连接服务器技术说明

技术交流 QQ:1264957104

## 一： socket发送数据协议：

协议头 | 校验 | 协议标识 | 数据类型 | 数据内容
---|---|---|---|---|---
4 Byte | 8 Byte | 64 Byte | 4 Byte | n Byte
 0 - 3 | 4 - 11 | 12 - 75 | 76 - 79 | 80 -n
   
 
  
 
序号 | 名称 | 类型 | 长度 | 说明
---|---|---|---|---
1 | 协议头 | 整数型 | 4 | 0-255 请求类型
2 | 校验 | 长整数型 | 8 | 默认为socket数据长度
3 | 协议标识 | 文本型 | 64 | 协议传输标识
4 | 数据类型 | 整数型 | 4 | 1-255 携带数据类型
5 | 数据内容 | 文本型 | n | 协议携带待处理数据
6 | 客户信息 | 文本型 | 20000 | Socket连接客户端信息
 
协议头：4字节    整数

校验：8字节    长整数

协议标识：64字节  文本

数据类型：   4字节  整数型 1-255

数据内容：无限
 


.程序集 Socket数据包, , 公开, 对socket接收数据进行拆包
.程序集变量 协议头, 整数型, , , 4Byte
.程序集变量 校验, 长整数型, , , 8Byte
.程序集变量 协议标识, 文本型, , , 64Byte
.程序集变量 数据类型, 整数型, , , 4Byte
.程序集变量 数据内容, 文本型, , , nByte
.程序集变量 客户信息, 文本型

 
 
####   1.协议
 
 
    
    .常量 客户端未登录标识, "<文本长度: 64>"
    .常量 服务器标识, "<文本长度: 64>"
    .常量 Socket_Timeout, "2"
    .常量 协议_心跳连接, "0", , 保持socket不自动断开
    .常量 协议_断开连接, "1", , 主动请求断开服务器，让服务器处理相应事务
    .常量 协议_请求服务, "2", , 获取设备列表、其他数据请求
    .常量 协议_数据传送, "3", , IM推送等
    .常量 协议_请求标识ID, "4", , 获得唯一标识义工使用
    .常量 协议_请求终端列表, "21", , 获得当前服务器已连接设备
    .常量 协议_请求断开终端, "23", , 根据标识断开某一设备连接，需要权限
    .常量 协议_注册登录设备, "31", , 不是用户注册登录，请求返回标识符
    .常量 数据类型_注册设备, "11"
    .常量 数据类型_登录设备, "12"
    .常量 协议_注册登录用户, "32", , 用户登录注册
    .常量 数据类型_注册用户, "11"
    .常量 数据类型_登录用户, "12"
    .常量 数据类型_被迫断开终端, "13", , 被其他设备挤掉
    .常量 协议_推送系统消息, "41", , 系统平台级推送
    .常量 协议_推送用户消息, "42", , 用户标准推送
    .常量 协议_推送批量用户消息, "43", , 控制端向指定用户推送消息
    .常量 数据类型_推送数据, "11"
    .常量 数据类型_推送指令, "12"
    .常量 数据类型_成功, "1"
    .常量 数据类型_失败, "2"
    .常量 数据类型_其它, "3"
    .常量 数据类型_终端列表, "11"





#### 2.协议详细


```
心跳连接 3s-100s,当服务器压力大时自动释放未主动发送数据客户端
协议头 ：协议头
校验 ：暂时为socket数据长度
协议标识 ：设备标识，登陆服务器会获得，请求标识不推荐，测试用
数据类型 ：作为反馈信息时默认1为成功2为失败3待定，作为数据请求传输时取值范围为11-255，保留十个数，不能设0，为了规避特殊原因0被屏蔽，使用0将无法传输数据。
数据内容  ：文本类json或xml，客户端和服务端要一至
客户信息  ：服务器存储信息，方便管理
```



## 二：客户端连接池
数据类型 ：ServerClient
 
序号 | 名称 | 类型 | 说明
---|---|---|---
1 | IP | 文本型 |客户端连接地址
2 | Flag | 文本型 | 协议传输标识
3 | Type | 整数型 | 1-255 携带数据类型,IM,push,其他
4 | LastTime | 日期时间型 | 当连接池过大，自动关闭较久未使用连接
5 | Tag | 文本型 | 扩展

    .   
    .成员 , 文本型, , , 127.0.0.1:8080
    .成员 , 文本型, , , 标识符
    .成员 , 整数型, , , 
    .成员 , 日期时间型   













## 三： 连接池模块：

 
ServerFunction
    
设置信息

创建ServerClient

获取信息

删除ServerClient

获取ServerClient

获取Client列表, 文本型 

获取Client数量, 整数型


 
## 四：客户端
#### 1.客户端常量 

    public class BaseData {
        public static final String SERVER_URL = "127.0.0.1";
        public static final int SERVER_PORT = 10080;
        public static final int SERVER_TIMEOUT = 2000;
        public static String ClientFlag = "252E90D2D36C4f7dB09EAEACCCA9D560BB83B5D5F9474db688890E899816933";
        public static final String ServerFlag = "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa";
        public static final String ClientNoFlag = "0000000000000000000000000000000000000000000000000000000000000000";
    
         public static class UserData {
            public static String UserName = "";
            public static String UserPwd = "";
        }
    
        public class Protocol {
            //协议_注册登录用户
            public static final int Protocol_RegLoginUser = 32;
            //协议_推送系统消息
            public static final int Protocol_PushSystemMsg = 41;
            //协议_推送用户消息
            public static final int Protocol_PushUserMsg = 42;
    
        }
    
        public class DataType {
            //数据类型_成功
            public static final int DataType_Success = 1;
            //数据类型_失败
            public static final int DataType_Error = 2;
            //数据类型_其他
            public static final int DataType_Other = 3;
            //数据类型_注册用户
            public static final int DataType_RegisterUser = 11;
            //数据类型_登录用户
            public static final int DataType_LoginUser = 12;
            //数据类型_终端列表
            public static final int DataType_ClientList = 11;
            //数据类型_推送消息
            public static final int DataType_PushMsg = 11;
            //数据类型_推送指令
            public static final int DataType_PushFunction = 12;
    
    
        }
    }
    
#### 2.监听回调
    //消息接收回调
    public interface onReceiveListener {
        public void onReceive(SocketData mSocketData);

        public void onError(Exception mE);
    }
    
    //服务状态监听
    public interface onServerStatusListener {
    
        //服务器连接成功
        public void onConnected();
        
        //设备登陆成功，如Push等
        public void onClientLogin();
        
        //用户登陆成功，如IM等
        public void onUserLogin();
        
        //socket断开，检查时间间隔由basedata的服务器timeout时间控制
        public void onDisconected();
        
        //数据接收发送或连接异常
        public void onError(Exception mE);
    }
    
#### 3.文件结构
ClientSocketService  核心服务类
