# LR22

Кириченко Антон

ЭВТ-70

Игровой Движок: Unity.

Лабораторная работа №22

Тема: Разработка игрового проекта MakeThemFall

Цель: приобрести навыки в разработке игрового проекта змейка

Ход работы:

1)	создаём сцену и импортируем ассеты

______________________________![image](https://user-images.githubusercontent.com/119228138/205002106-0e3fdbc4-47c6-4d90-a0b4-402741d5d273.png)

 
______________________________Рисунок 22.1 спрайт игрового персонажа

```
1.	скрипт для прыжка Player
public class Player : MonoBehaviour
{
    public Vector2 xValue;
    public bool changeDirectionBool, leftFacing, changeIt,easing;
    Animator animator;
    Vector3 desiredPosition;
    // Start is called before the first frame update
    void Start()
    {
        DesiredPosition();
        changeDirection();
        animator = GetComponent<Animator>();
    }
    // Update is called once per frame
    void Update()
    {
        if (changeIt)
        {
            if (easing)
            {
                transform.position = Vector3.Lerp(transform.position, desiredPosition, .1f);
            }
            else
            {
                transform.position = desiredPosition;
            }
            if (Mathf.Abs(desiredPosition.x - transform.position.x) <= 0.1f)
            {
                Debug.Log("Changing gavity");
                changeIt = false;
            }
        }
        animator.SetBool("ChangeDirection", changeDirectionBool);
    }
    public void CallThisMethod()
    {
        DesiredPosition();
        changeDirection();
    }
    public void DesiredPosition()
    {
        desiredPosition = new Vector2(changeDirectionBool ? xValue.x : xValue.y, transform.position.y);
    }
    void changeDirection()
    {
        changeDirectionBool = !changeDirectionBool;
        if (changeDirectionBool != leftFacing)
        {
            changeIt = true;
            leftFacing = changeDirectionBool;
        }
        Debug.Log(transform.position.x);
    }
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.tag == "Spike")
        {
            Debug.Log("dead Player");
            FindObjectOfType<GameManager>().RestartScene();
        }
    }
}
```
2.	 созданы шипы, а также скрипт для них

___________________![image](https://user-images.githubusercontent.com/119228138/205002252-eaa2409a-25ed-4bff-b969-6b32650c6cc5.png)


___________________Рисунок 22.2 Игровое поле

```
Скрипт для шипов Spike
public class Spike : MonoBehaviour
{
    public float moveSpeed;
    public Vector2 minMaxYValue;
    Vector2 pos;
    // Start is called before the first frame update
    void Start()
    {
        
    }
    // Update is called once per frame
    void Update()
    {
        transform.position += Vector3.up * moveSpeed * Time.deltaTime;
        if (transform.position.y > minMaxYValue.x)
        {
            pos = transform.position;
            pos.x = transform.position.x;
            pos.y = minMaxYValue.y;
            transform.position = pos;
            ShowOrHide();
        }
    }
    void ShowOrHide()
    {
        if (Random.value > 0.5f)
        {
            Debug.Log("Disabling");
            transform.GetChild(0).gameObject.SetActive(false);
            return;
        }
        transform.GetChild(0).gameObject.SetActive(true);
    }
}
```
3.	Была создана вторая часть площадки и второй персонаж

___________________![image](https://user-images.githubusercontent.com/119228138/205002299-6202d8b4-61f1-4cec-a96f-81ed69c7bb48.png)

___________________Рисунок 22.3 Игровое поле после добавления второй площадки и второго персонажа.

```
4.	 создали отдельный колайдер для  игрового поля и прикреплен к держателю полей
Скрипт ColliderGO
public class ColliderGO : MonoBehaviour
{
    public Transform PlayerTransform;
    public void CallPlayerMethod()
    {
        Debug.Log(PlayerTransform.name);
        PlayerTransform.GetComponent<Player>().CallThisMethod();
    }
}
5.	создали скрипт ClickChecker для нажатия на правую и левую половину поля 
public class ClickChecker : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }
    // Update is called once per frame
    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            Vector2 pos = Camera.main.ScreenToWorldPoint(Input.mousePosition);
            RaycastHit2D hit = Physics2D.Raycast(pos, Vector2.zero);
            if (hit)
            {
                if (hit.transform.name == "LeftSide")
                {
                    hit.collider.GetComponent<ColliderGO>().CallPlayerMethod();
                }
                else if (hit.transform.name == "RightSide")
                {
                    hit.collider.GetComponent<ColliderGO>().CallPlayerMethod();
                }
                else { Debug.Log("Unknown hit from click Checker"); return; }
            }
        }
    }
}
```
6.	создана анимация для плавности передвижения персонажа

___________________![image](https://user-images.githubusercontent.com/119228138/205002346-ed231e76-ddd4-49b0-a78a-f7c22c254ad7.png)

_________________________________Рисунок 22.4 кадр из создания анимации.

```
7.	создан скрипт ColorCombinatoin для наложения цвета на разные площадки
[System.Serializable]
public class ColorCombination
{
    public Color[] ColorToChoose;
}

8.	 создан скрипт ColorChooser для рандомизации цветов 
public class ColorChooser : MonoBehaviour
{
    public GameObject[] imagesGO;
    public ColorCombination[] chooseColor;
    void Awake()
    {
        int randomNo = Random.Range(0, chooseColor.Length + 1);
        imagesGO[0].GetComponent<SpriteRenderer>().color = chooseColor[randomNo].ColorToChoose[0];
        imagesGO[1].GetComponent<SpriteRenderer>().color = chooseColor[randomNo].ColorToChoose[1];
    }
    void Update()
    {
    }
}
9.	 создан GameManager для перезапуска игры при проигрыше.
public class GameManager : MonoBehaviour
{
    public void RestartScene()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}
```
Вывод: В ходе проделанной работы была разработана игра MakeThemFall

[22.Make.Them.Fall.zip](https://github.com/Userfall3000/LR-22/files/10132129/22.Make.Them.Fall.zip)

