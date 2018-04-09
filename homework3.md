# 游戏对象与图形基础
### 一. 操作与总结： ###  
- 参考 Fantasy Skybox FREE 构建自己的游戏场景  
在创建完Terrian对象之后，将下载好的Skyboxes文件导入到Assets当中：  
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d4.png)  
之后为整个场景添加背景（天空），在Main Camera中添加一个Skybox属性，选择想要的背景：  
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d2.png)  
Terrian的属性中可以涂刷地形等：  
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d3.png)  
之后粗略地建造了下地形，效果如下图：
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d1.png)  
   
- 总结游戏对象的使用：  
添加背景可以选择在主摄像机中添加  
地形一般需要创建Terrian对象，然后在上面修改效果  
在Assets Store中可以下载自己想要的资源  
树木等可以直接导入到Assets中（系统自带）  
### 二. 编程实践：牧师与魔鬼 动作分离版  ###  
对于ppt要求的理解是将动作抽象出来，所以本次文件目录如下：  
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d5.png)  
实现效果和上周相同：  
![avatar](https://github.com/MockingT/3D_Game-3/blob/master/picture/3d6.png)  
其中actionmanager.cs中包含了船的运动分解代码； Controlle.cs文件中删了枚举类型的使用，改用IQueryGameStatus接口来判断当前状态；，model.cs文件大致和上次类似。  
具体代码如下：  
- View.cs  

      using UnityEngine;
      using System.Collections;
      using Com.Mygame;

      public class View : MonoBehaviour
      {

          float width;
          float height;
          float castw(float a)
          {
              return (Screen.width - width) / a;
          }
          float casth(float a)
          {
              return (Screen.height - height) / a;
          }

          GameSceneController scene;
          IQueryGameStatus state;
          IUserActions action;

          // Use this for initialization  
          void Start()
          {
              scene = GameSceneController.GetInstance();
              state = GameSceneController.GetInstance() as IQueryGameStatus;
              action = GameSceneController.GetInstance() as IUserActions;
          }

          private void OnGUI()
          {
              GUI.skin.label.fontSize = 20;
              width = Screen.width / 12;
              height = Screen.height / 12;

              string message = state.isMessage();
              // win or fail
              if (message != "")
              {
                  if (GUI.Button(new Rect(castw(2f), casth(2f), width, height), message))
                  {
                      action.restart();
                  }
              }
              else
              {
                  if (GUI.Button(new Rect(castw(2f), casth(6f), width, height), "GO"))
                  {
                      action.boat_move();
                  }
                  if (GUI.Button(new Rect(castw(2.5f), casth(1f), width, height), "OFF"))
                  {
                      action.Left_off_boat();
                  }
                  if (GUI.Button(new Rect(castw(1.6f), casth(1f), width, height), "OFF"))
                  {
                      action.Right_off_boat();
                  }
                  if (GUI.Button(new Rect(castw(1.2f), casth(4f), width, height), "ON"))
                  {
                      action.priest_end();
                  }
                  if (GUI.Button(new Rect(castw(1f), casth(4f), width, height), "ON"))
                  {
                      action.devil_end();
                  }
                  if (GUI.Button(new Rect(castw(10f), casth(4f), width, height), "ON"))
                  {
                      action.devil_start();
                  }
                  if (GUI.Button(new Rect(castw(4.5f), casth(4f), width, height), "ON"))
                  {
                      action.priest_start();
                  }
              }
          }
      }  
      
- Controller.cs  

      using UnityEngine;
      using System.Collections;
      using Com.Mygame;

      namespace Com.Mygame
      {
          public interface IUserActions
          {
              void priest_start();
              void priest_end();
              void devil_start();
              void devil_end();
              void boat_move();
              void Left_off_boat();
              void Right_off_boat();
              void restart();
          }

          public interface IQueryGameStatus
          {
              bool isMoving();
              void setMoving(bool state);
              string isMessage();
              void setMessage(string message);
          }

          public class GameSceneController : System.Object, IUserActions, IQueryGameStatus
          {

              private static GameSceneController _instance;
              private Model _gen_game_obj;
              private bool moving = false;
              private string message = "";

              public static GameSceneController GetInstance()
              {
                  if (null == _instance)
                  {
                      _instance = new GameSceneController();
                  }
                  return _instance;
              }

              // registration  

              public Model getModel()
              {
                  return _gen_game_obj;
              }

              internal void setModel(Model ggo)
              {
                  if (null == _gen_game_obj)
                  {
                      _gen_game_obj = ggo;
                  }
              }

              // IQueryGameStatus  
              public bool isMoving() { return moving; }
              public void setMoving(bool state) { this.moving = state; }
              public string isMessage() { return message; }
              public void setMessage(string message) { this.message = message; }

              // IUserActions  
              public void priest_start() { _gen_game_obj.priestStartOnBoat(); }
              public void priest_end() { _gen_game_obj.priestEndOnBoat(); }
              public void devil_start() { _gen_game_obj.devilStartOnBoat(); }
              public void devil_end() { _gen_game_obj.devilEndOnBoat(); }
              public void boat_move() { _gen_game_obj.boat_move(); }
              public void Left_off_boat() { _gen_game_obj.getOffTheBoat(0); }
              public void Right_off_boat() { _gen_game_obj.getOffTheBoat(1); }

              public void restart()
              {
                  moving = false;
                  message = "";
                  Application.LoadLevel(Application.loadedLevelName);
              }
          }


      }

      public class BaseCode : MonoBehaviour
      {

          public string gameName;
          public string gameRule;

          void Start()
          {
              GameSceneController my = GameSceneController.GetInstance();
          }
      }  
      
- actionmanager.cs  

      using Com.Mygame;
      using System.Collections;
      using System.Collections.Generic;
      using UnityEngine;

      public class actionmanager : MonoBehaviour {
          public interface IU3dActionCompleted
          {
              void OnActionCompleted(U3dAction action);
          }

          public class U3dActionException : System.Exception { }

          public class U3dAction : MonoBehaviour
          {
              public void Free()
              {
                  Destroy(this);
              }
          }

          public class U3dActionAuto : U3dAction { }

          public class U3dActionMan : U3dAction { }

          public class ActionManager : System.Object
          {
              private static ActionManager _instance;

              public static ActionManager GetInstance()
              {
                  if (_instance == null)
                  {
                      _instance = new ActionManager();
                  }
                  return _instance;
              }

              // ApplyMoveToAction  
              public U3dAction ApplyMoveToAction(GameObject obj, Vector3 target, float speed, IU3dActionCompleted completed)
              {
                  MoveToAction ac = obj.AddComponent<MoveToAction>();
                  ac.setting(target, speed, completed);
                  return ac;
              }

              public U3dAction ApplyMoveToAction(GameObject obj, Vector3 target, float speed)
              {
                  return ApplyMoveToAction(obj, target, speed, null);
              }

              // ApplyMoveToYZAction  
              public U3dAction ApplyMoveToYZAction(GameObject obj, Vector3 target, float speed, IU3dActionCompleted completed)
              {
                  MoveToYZAction ac = obj.AddComponent<MoveToYZAction>();
                  ac.setting(obj, target, speed, completed);
                  return ac;
              }

              public U3dAction ApplyMoveToYZAction(GameObject obj, Vector3 target, float speed)
              {
                  return ApplyMoveToYZAction(obj, target, speed, null);
              }
          }

          public class MoveToAction : U3dActionAuto
          {
              public Vector3 target;
              public float speed;

              private IU3dActionCompleted monitor = null;

              public void setting(Vector3 target, float speed, IU3dActionCompleted monitor)
              {
                  this.target = target;
                  this.speed = speed;
                  this.monitor = monitor;
                  GameSceneController.GetInstance().setMoving(true);
              }

              void Update()
              {
                  float step = speed * Time.deltaTime;
                  transform.position = Vector3.MoveTowards(transform.position, target, step);

                  // Auto Destroy After Completed  
                  if (transform.position == target)
                  {
                      GameSceneController.GetInstance().setMoving(false);
                      if (monitor != null)
                      {
                          monitor.OnActionCompleted(this);
                      }
                      Destroy(this);
                  }
              }
          }

          /* MoveToYZAction is a combination of two MoveToAction(s) 
           * It moves on a single shaft(Y or Z) each time 
           */
          public class MoveToYZAction : U3dActionAuto, IU3dActionCompleted
          {
              public GameObject obj;
              public Vector3 target;
              public float speed;

              private IU3dActionCompleted monitor = null;

              public void setting(GameObject obj, Vector3 target, float speed, IU3dActionCompleted monitor)
              {
                  this.obj = obj;
                  this.target = target;
                  this.speed = speed;
                  this.monitor = monitor;
                  GameSceneController.GetInstance().setMoving(true);

                  /* If obj is higher than target, move to target.z first, then move to target.y 
                   * If obj is lower than target, move to target.y first, then move to target.z 
                   * The turn is implemented through callback function 
                   */
                  if (target.y < obj.transform.position.y)
                  {
                      Vector3 targetZ = new Vector3(target.x, obj.transform.position.y, target.z);
                      ActionManager.GetInstance().ApplyMoveToAction(obj, targetZ, speed, this);
                  }
                  else
                  {
                      Vector3 targetY = new Vector3(obj.transform.position.x, target.y, target.z);
                      ActionManager.GetInstance().ApplyMoveToAction(obj, targetY, speed, this);
                  }
              }

              // Implement the turn  
              public void OnActionCompleted(U3dAction action)
              {
                  // Note not calling this callback again!  
                  ActionManager.GetInstance().ApplyMoveToAction(obj, target, speed, null);
              }

              void Update()
              {
                  // Auto Destroy After Completed  
                  if (obj.transform.position == target)
                  {
                      GameSceneController.GetInstance().setMoving(false);
                      if (monitor != null)
                      {
                          monitor.OnActionCompleted(this);
                      }
                      Destroy(this);
                  }
              }
          }
      }  
      
 - Model.cs  
 
 
         using UnityEngine;
         using System.Collections;
         using System.Collections.Generic;
         using Com.Mygame;

         public class Model : MonoBehaviour
         {

             Stack<GameObject> priests_start = new Stack<GameObject>();
             Stack<GameObject> priests_end = new Stack<GameObject>();
             Stack<GameObject> devils_start = new Stack<GameObject>();
             Stack<GameObject> devils_end = new Stack<GameObject>();

             GameObject[] boat = new GameObject[2];
             GameObject boat_obj;
             int side = 1;               // side records where boat docks  

             Vector3 boatStartPos = new Vector3(-11, -3, 0); // boat start from left
             Vector3 boatEndPos = new Vector3(-1, -3, 0); // boat end at right
             Vector3 shoreStartPos = new Vector3(-19, -5, 0); // the left shore
             Vector3 shoreEndPos = new Vector3(8, -5, 0);  // the right shore
             Vector3 priestsStartPos = new Vector3(-18, -1.5f, 0);
             Vector3 priestsEndPos = new Vector3(7, -1.5f, 0);
             Vector3 devilsStartPos = new Vector3(-24, -1.5f, 0);
             Vector3 devilsEndPos = new Vector3(13, -1.5f, 0);
             // the original postion

             public float speed = 50f;

             float gap = 1.5f;

             void Start()
             {
                 GameSceneController.GetInstance().setModel(this);
                 loadSrc();
             }

             void loadSrc()
             {
                 // shore  
                 Instantiate(Resources.Load("Prefabs/Shore"), shoreStartPos, Quaternion.identity);
                 Instantiate(Resources.Load("Prefabs/Shore"), shoreEndPos, Quaternion.identity);
                 // boat  
                 boat_obj = Instantiate(Resources.Load("Prefabs/Boat"), boatStartPos, Quaternion.identity) as GameObject;
                 // priests & devils  
                 for (int i = 0; i < 3; ++i)
                 {
                     GameObject priest = Instantiate(Resources.Load("Prefabs/Priest")) as GameObject;
                     priest.transform.position = getCharacterPosition(priestsStartPos, i);
                     priest.tag = "Priest";
                     priests_start.Push(priest);
                     GameObject devil = Instantiate(Resources.Load("Prefabs/Devil")) as GameObject;
                     devil.transform.position = getCharacterPosition(devilsStartPos, i);
                     devil.tag = "Devil";
                     devils_start.Push(devil);
                 }
                 // light  
                 Instantiate(Resources.Load("Prefabs/Light"));
             }

             int boatCapacity()
             {
                 int capacity = 0;
                 for (int i = 0; i < 2; ++i)
                 {
                     if (boat[i] == null) capacity++;
                 }
                 return capacity;
             }

             void getOnTheBoat(GameObject obj)
             {
                 if (boatCapacity() != 0)
                 {
                     obj.transform.parent = boat_obj.transform;
                     Vector3 target = new Vector3();
                     if (boat[0] == null)
                     {
                         boat[0] = obj;
                         target = boat_obj.transform.position + boatStartPos + new Vector3(0, 4.5f, 0);
                     }
                     else
                     {
                         boat[1] = obj;
                         target = boat_obj.transform.position + boatEndPos + new Vector3(0, 4.5f,0);
                     }
                     actionmanager.ActionManager.GetInstance().ApplyMoveToYZAction(obj, target, speed);
                 }
             }

             public void boat_move()
             {
                 if (boatCapacity() != 2)
                 {
                     if (side == 1)
                     {
                         actionmanager.ActionManager.GetInstance().ApplyMoveToAction(boat_obj, boatEndPos, speed);
                         side = 2;
                     }
                     else if (side == 2)
                     {
                         actionmanager.ActionManager.GetInstance().ApplyMoveToAction(boat_obj, boatStartPos, speed);
                         side = 1;
                     }
                 }
             }

             public void getOffTheBoat(int bside)
             {
                 if (boat[bside] != null)
                 {
                     boat[bside].transform.parent = null;
                     Vector3 target = new Vector3();
                     if (side == 1)
                     {
                         if (boat[bside].tag == "Priest")
                         {
                             priests_start.Push(boat[bside]);
                             target = getCharacterPosition(priestsStartPos, priests_start.Count - 1);
                         }
                         else if (boat[bside].tag == "Devil")
                         {
                             devils_start.Push(boat[bside]);
                             target = getCharacterPosition(devilsStartPos, devils_start.Count - 1);
                         }
                     }
                     else if (side == 2)
                     {
                         if (boat[bside].tag == "Priest")
                         {
                             priests_end.Push(boat[bside]);
                             target = getCharacterPosition(priestsEndPos, priests_end.Count - 1);
                         }
                         else if (boat[bside].tag == "Devil")
                         {
                             devils_end.Push(boat[bside]);
                             target = getCharacterPosition(devilsEndPos, devils_end.Count - 1);
                         }
                     }
                     actionmanager.ActionManager.GetInstance().ApplyMoveToYZAction(boat[bside], target, speed);
                     boat[bside] = null;
                 }
             }

             public void priestStartOnBoat()
             {
                 if (priests_start.Count != 0 && boatCapacity() != 0 && side == 1)
                     getOnTheBoat(priests_start.Pop());
             }

             public void priestEndOnBoat()
             {
                 if (priests_end.Count != 0 && boatCapacity() != 0 && side == 2)
                     getOnTheBoat(priests_end.Pop());
             }

             public void devilStartOnBoat()
             {
                 if (devils_start.Count != 0 && boatCapacity() != 0 && side == 1)
                     getOnTheBoat(devils_start.Pop());
             }

             public void devilEndOnBoat()
             {
                 if (devils_end.Count != 0 && boatCapacity() != 0 && side == 2)
                     getOnTheBoat(devils_end.Pop());
             }

             Vector3 getCharacterPosition(Vector3 pos, int index)
             {
                 return new Vector3(pos.x + gap * index, pos.y, pos.z);
             }

             void check()
             {
                 GameSceneController scene = GameSceneController.GetInstance();
                 int pOnb = 0, dOnb = 0;
                 int priests_s = 0, devils_s = 0, priests_e = 0, devils_e = 0;

                 if (priests_end.Count == 3 && devils_end.Count == 3)
                 {
                     scene.setMessage("Win!");
                     return;
                 }

                 for (int i = 0; i < 2; ++i)
                 {
                     if (boat[i] != null && boat[i].tag == "Priest") pOnb++;
                     else if (boat[i] != null && boat[i].tag == "Devil") dOnb++;
                 }
                 if (side == 1)
                 {
                     priests_s = priests_start.Count + pOnb;
                     devils_s = devils_start.Count + dOnb;
                     priests_e = priests_end.Count;
                     devils_e = devils_end.Count;
                 }
                 else if (side == 2)
                 {
                     priests_s = priests_start.Count;
                     devils_s = devils_start.Count;
                     priests_e = priests_end.Count + pOnb;
                     devils_e = devils_end.Count + dOnb;
                 }
                 if ((priests_s != 0 && priests_s < devils_s) || (priests_e != 0 && priests_e < devils_e))
                 {
                     scene.setMessage("Lose!");
                 }
             }

             void Update()
             {
                 check();
             }

         }


