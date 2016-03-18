---
title: Android 调用PowerManager 实现延时锁屏

---
今天公司项目有一个需求，就是再语音通话过程中保持聊天界面屏幕常亮。于是查阅资料，发现使用Android 的 *WakeLock* 机制可以很轻松的实现。
  首先我们要清楚，在手机锁屏的时候，android 系统为了达到省电的和减少CPU的消耗，在锁屏的一段时间后进入休眠的状态，这时候Android 的CPU会保持在一个功耗较低状态。那么如何在Android 休眠的状态执行消息的接收并且点亮屏幕呢？
  直接上代码：
  
```android
    WakeLock wakeLock = null;  
        //获取电源锁，保持该服务在屏幕熄灭时仍然获取CPU时，保持运行  
        private void acquireWakeLock()  
        {  
            if (null == wakeLock)  
            {  
                PowerManager pm = (PowerManager)this.getSystemService(Context.POWER_SERVICE);  
                wakeLock = pm.newWakeLock(PowerManager.PARTIAL_WAKE_LOCK|PowerManager.ON_AFTER_RELEASE, "PostLocationService");  
                if (null != wakeLock)  
                {  
                    wakeLock.acquire();  
                }  
            }  
        }  
        //释放设备电源锁  
        private void releaseWakeLock()  
        {  
            if (null != wakeLock)  
            {  
                wakeLock.release();  
                wakeLock = null;  
            }  
        }
    }
```
  



