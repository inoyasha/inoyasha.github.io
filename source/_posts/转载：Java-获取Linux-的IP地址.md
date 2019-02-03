title: 转载：Java 获取Linux 的IP地址
copyright: false
author: 郭小黑
tags: []
categories:
  - java web
date: 2019-02-03 17:09:00
---

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=28875230&auto=0&height=66"></iframe>

#### 前言

在java web项目中，我们总会有获取服务器ip地址的需求，下面给出获取操作系统ip地址方法。

<!--more-->

#### 实际编码

```
package com.xx.utils;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.servlet.http.HttpServletRequest;
import java.net.InetAddress;
import java.net.NetworkInterface;
import java.net.SocketException;
import java.net.UnknownHostException;
import java.util.Enumeration;

/**
 * Description:
 *
 * @author guoyupeng [2018/7/18]
 */
public final class WebToolUtils {

    /**
     * 系统os为windows的标志
     */
    private static final String OS_WINDOWS = "windows";

    /**
     * 获取客户端ip 不知道的ip地址
     */
    private static final String UNKNOWN_IP = "unknown";

    /**
     * logger
     */
    private static final Logger logger = LoggerFactory.getLogger(WebToolUtils.class);

    /**
     * 获取本地ip地址
     *
     * @return
     */
    public static String getLoacalIp() throws UnknownHostException, SocketException {
        if (isWindowsOs()) {
            return getLocalHostName();
        } else {
            return getLinuxLocalIp();
        }
    }

    /**
     * 是否windows系统
     *
     * @return
     */
    public static boolean isWindowsOs() {
        boolean isWindows = false;
        String osName = System.getProperty("os.name");
        if (osName.toLowerCase().indexOf(OS_WINDOWS) > -1) {
            isWindows = true;
        }
        return isWindows;
    }

    /**
     * 获取本机ip地址
     *
     * @return
     * @throws UnknownHostException 不知道的主机名称异常
     */
    public static String getLocalHostName() throws UnknownHostException {
        return InetAddress.getLocalHost().getHostName();
    }


    /**
     * 获取Linux下的IP地址
     *
     * @return IP地址
     * @throws SocketException
     */
    private static String getLinuxLocalIp() throws SocketException {
        String ip = "";
        try {
            for (Enumeration<NetworkInterface> en = NetworkInterface.getNetworkInterfaces(); en
                    .hasMoreElements(); ) {
                NetworkInterface intf = en.nextElement();
                String name = intf.getName();
                if (!name.contains("docker") && !name.contains("lo")) {
                    for (Enumeration<InetAddress> enumIpAddr = intf.getInetAddresses(); enumIpAddr
                            .hasMoreElements(); ) {
                        InetAddress inetAddress = enumIpAddr.nextElement();
                        if (!inetAddress.isLoopbackAddress()) {
                            String ipAddress = inetAddress.getHostAddress().toString();
                            if (!ipAddress.contains("::") && !ipAddress.contains("0:0:")
                                    && !ipAddress.contains("fe80")) {
                                ip = ipAddress;
                                logger.debug("ip地址为：{}", ipAddress);
                            }
                        }
                    }
                }
            }
        } catch (SocketException ex) {
            logger.error("获取ip地址异常", ex.getMessage());
            ip = "127.0.0.1";
        }
        return ip;
    }



    /**
     * 获取用户真实IP地址，不使用request.getRemoteAddr();的原因是有可能用户使用了代理软件方式避免真实IP地址,
     * <p>
     * 可是，如果通过了多级反向代理的话，X-Forwarded-For的值并不止一个，而是一串IP值，究竟哪个才是真正的用户端的真实IP呢？
     * 答案是取X-Forwarded-For中第一个非unknown的有效IP字符串。
     * <p>
     * 如：X-Forwarded-For：192.168.1.110, 192.168.1.120, 192.168.1.130,
     * 192.168.1.100
     * <p>
     * 用户真实IP为： 192.168.1.110
     *
     * @param request
     * @return
     */
    public static String getIpAddress(HttpServletRequest request) {
        String ip = request.getHeader("x-forwarded-for");
        if (ip == null || ip.length() == 0 || UNKNOWN_IP.equalsIgnoreCase(ip)) {
            ip = request.getHeader("Proxy-Client-IP");
        }
        if (ip == null || ip.length() == 0 || UNKNOWN_IP.equalsIgnoreCase(ip)) {
            ip = request.getHeader("WL-Proxy-Client-IP");
        }
        if (ip == null || ip.length() == 0 || UNKNOWN_IP.equalsIgnoreCase(ip)) {
            ip = request.getHeader("HTTP_CLIENT_IP");
        }
        if (ip == null || ip.length() == 0 || UNKNOWN_IP.equalsIgnoreCase(ip)) {
            ip = request.getHeader("HTTP_X_FORWARDED_FOR");
        }
        if (ip == null || ip.length() == 0 || UNKNOWN_IP.equalsIgnoreCase(ip)) {
            ip = request.getRemoteAddr();
        }
        return ip;
    }

}
```

原文地址：[Java 获取Linux 的IP地址](https://www.cnblogs.com/raphael5200/p/5996464.html)