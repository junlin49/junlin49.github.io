---
title: Vuforia-阶段Demo小总结
date: 2020-04-02 18:28:37
tags:
- Vuforia
---

记录学习的demo

<!--more-->

---

### 需求

---

实现如下效果：

- 相机对准黑桃K后，出现人物
- 人物先从纸牌中出现，后跳舞，再奔跑，最后攻击
- 有特效要求
- 要有截屏，闪光灯，自动对焦功能
- 双击人物，人物消失
- 可以两指放大，缩小
- 可旋转

---

### 实现

---

1. 自动对焦

   ```c#
    private void Start()
       {
           var vuforia = VuforiaARController.Instance;
           vuforia.RegisterVuforiaStartedCallback(OnVuforiaStarted);// Vuforia启动完成时的回调函数
           vuforia.RegisterOnPauseCallback(OnVuforiaPaused);//Vuforia暂停时的回调函数
       }
   
       private void OnVuforiaStarted()
       {
           //程序启动，自动对焦
           CameraDevice.Instance.SetFocusMode(CameraDevice.FocusMode.FOCUS_MODE_CONTINUOUSAUTO);//连续自动对焦
       }
   
       private void OnVuforiaPaused(bool isPaused)
       {
   
       }
   
       public void SetCameraFocus()//手动单次对焦
       {
           CameraDevice.Instance.SetFocusMode(CameraDevice.FocusMode.FOCUS_MODE_TRIGGERAUTO);//手动单次对焦
       }
   ```

   

2. 闪光灯

   ```c#
   public void SetFlash()//设置闪光灯
       {
           CameraDevice.Instance.SetFlashTorchMode(openFlash);
           openFlash = !openFlash;
       }
   ```

   

3. 双击消失

   ```c#
    private float touchTime;
       private bool touchNew = false;
       private void Update()
       {
           if (Input.GetMouseButton(0))//有触屏
           {
               //从点击位置发射射线
               Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
              //接受返回信息
               RaycastHit hitInfor;
   
               if (Physics.Raycast(ray,out hitInfor))//如果接受到信息
               {
                 //  if(Input.touchCount == 1 && Input.GetTouch(0).phase == TouchPhase.Began)//1根手指点击，且刚刚点击
                  // {
                       //双击
                       //if(Input.GetTouch(0).tapCount == 2)//双击
                       //{
                       //    Destroy(hitInfor.collider.gameObject);//射线射到的collider所属的游戏物体，销毁
                       //}
   
                       //长按aixi，销毁
                       if (Input.touchCount == 1)//1根手指按
                       {
                           Touch touch = Input.GetTouch(0);//第一根触摸的手指
                           if (touch.phase == TouchPhase.Began)//刚按，A finger touched the screen.
                       {
                               touchNew = true;
                               touchTime = Time.time;//记录开始按的时候的时间
                           }
                           else if (touch.phase == TouchPhase.Stationary)//按住静止不动；A finger is touching the screen but hasn't moved.
                       {
                             if (touchNew == true && Time.time-touchTime>1)//长按1s后
                               {
                                   touchNew = false;
                                   Destroy(hitInfor.collider.gameObject);
                               }
                           }
                           else
                           {
                               touchNew = false;
                           }
                       }
                  // }
               }
           }
       }
   ```

   

4. 旋转放缩

   ```c#
   private void Update()
       {
           if (Input.GetMouseButton(0))//触屏了
           {
               if (Input.touchCount == 1)//一个手指头
               {
                   if (Input.GetTouch(0).phase == TouchPhase.Moved)//状态移动
                   {
                       this.transform.Rotate(0, -xRotationSpeed * Time.deltaTime * Input.GetAxis ("Mouse X"),0,Space.World);
                   }
               }
   
               if (Input.touchCount == 2)
               {
                   if (Input.GetTouch(0).phase == TouchPhase.Moved || Input.GetTouch(1).phase == TouchPhase.Moved)
                   {
                       Vector2 temPositon1 = Input.GetTouch(0).position;
                       Vector2 temPositon2 = Input.GetTouch(1).position;
                       if (IsLarge(oldPositon1,oldPositon2,temPositon1,temPositon2))
                       {
                           //arCamera.fieldOfView -= arCamera.fieldOfView*0.01*
                           this.transform.localScale = new Vector3(
                               this.transform.localScale.x * scaleOut, this.transform.localScale.y * scaleOut, this.transform.localScale.z * scaleOut);
                       }
                       else
                       {
                           this.transform.localScale = new Vector3(
                              this.transform.localScale.x * scaleIn, this.transform.localScale.y * scaleIn, this.transform.localScale.z * scaleIn);
                       }
                       oldPositon1 = temPositon1;
                       oldPositon2 = temPositon2;
                   }
               }
           }
       }
   
       private bool IsLarge(Vector2 oldPoint1, Vector2 oldPoint2 , Vector2 newPoint1 , Vector2 newPoint2)
       {
           if (Vector2.Distance(oldPoint1, oldPoint2) < Vector2.Distance(newPoint1,newPoint2))
           {
               return true;
           }
           else
           {
               return false;
           }
       }
   ```

   ---

   ### 演示

   ---

   [![8OLECj.jpg](https://s1.ax1x.com/2020/03/25/8OLECj.jpg)](https://imgchr.com/i/8OLECj)

