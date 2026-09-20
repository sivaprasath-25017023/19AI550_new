# Ex.No: 10  Implementation of 2D/3D game -------------------
### DATE: 20.09.2026                                                            
### REGISTER NUMBER : 212224243007
### AIM: 
To develop a Coin Collector 2D Game in Unity
### Algorithm:
```
1.Step 1: Start the Unity game.

Step 2: Create the main camera and set it to 2D orthographic mode.

Step 3: Create the Game Manager to control score, winning and game-over conditions.

Step 4: Create the player with:

Sprite Renderer Rigidbody2D Box Collider 2D Player Controller script

Step 5: Create 10 coins at different positions.

Step 6: When the player touches a coin:

Increase the score by 1. Destroy the collected coin.

Step 7: Create an enemy that continuously moves toward the player.

Step 8: When the enemy touches the player, display GAME OVER.

Step 9: When the player collects all 10 coins, display YOU WIN.

Step 10: Provide a Restart button to reload the current scene.


```  
### Program:

## PlayerController.cs
```
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float speed = 5f;
    private Rigidbody2D rb;
    private Vector2 movement;

    private void Awake()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    private void Update()
    {
        movement = new Vector2(
            Input.GetAxisRaw("Horizontal"),
            Input.GetAxisRaw("Vertical")
        ).normalized;
    }

    private void FixedUpdate()
    {
        if (rb != null)
            rb.MovePosition(
                rb.position + movement * speed * Time.fixedDeltaTime
            );
    }
}
```
## Coin.cs
```
using UnityEngine;

public class Coin : MonoBehaviour
{
    public int value = 1;

    private void OnTriggerEnter2D(Collider2D other)
    {
        if (other.CompareTag("Player") &&
            GameManager.Instance != null)
        {
            GameManager.Instance.AddScore(value);
            Destroy(gameObject);
        }
    }
}
```
## Enemy.cs
```
using UnityEngine;

public class Enemy : MonoBehaviour
{
    public float speed = 1.5f;
    private Transform player;

    private void Update()
    {
        if (GameManager.Instance != null &&
            GameManager.Instance.GameFinished)
            return;

        if (player == null)
        {
            GameObject p =
                GameObject.FindGameObjectWithTag("Player");

            if (p != null)
                player = p.transform;

            return;
        }

        Vector3 direction =
            (player.position - transform.position).normalized;

        transform.position +=
            direction * speed * Time.deltaTime;
    }

    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Player") &&
            GameManager.Instance != null)
        {
            GameManager.Instance.GameOver();
        }
    }
}
```
## GameBootstrap.cs
```
using UnityEngine;

public static class GameBootstrap
{
    [RuntimeInitializeOnLoadMethod(
        RuntimeInitializeLoadType.AfterSceneLoad)]
    private static void StartGame()
    {
        if (GameObject.Find("GameManager") != null)
            return;

        CreateGame();
    }

    private static void CreateGame()
    {
        Camera cam = Camera.main;

        if (cam == null)
        {
            GameObject cameraObject =
                new GameObject("Main Camera");

            cameraObject.tag = "MainCamera";

            cam = cameraObject.AddComponent<Camera>();
            cameraObject.AddComponent<AudioListener>();
        }

        cam.orthographic = true;
        cam.orthographicSize = 5.5f;

        cam.transform.position =
            new Vector3(0f, 0f, -10f);

        cam.backgroundColor =
            new Color(0.08f, 0.12f, 0.08f);

        // Game Manager
        GameObject manager =
            new GameObject("GameManager");

        manager.AddComponent<GameManager>();

        // Player
        GameObject player =
            CreateSprite(
                "Player",
                new Vector2(0f, 0f),
                Color.cyan,
                0.65f
            );

        player.tag = "Player";

        Rigidbody2D playerBody =
            player.AddComponent<Rigidbody2D>();

        playerBody.gravityScale = 0f;
        playerBody.freezeRotation = true;

        playerBody.collisionDetectionMode =
            CollisionDetectionMode2D.Continuous;

        player.AddComponent<BoxCollider2D>();
        player.AddComponent<PlayerController>();

        // Coins
        Vector2[] coinPositions =
        {
            new Vector2(-4f, 3f),
            new Vector2(-2f, 2f),
            new Vector2(0f, 3f),
            new Vector2(2f, 2f),
            new Vector2(4f, 3f),

            new Vector2(-4f, -2f),
            new Vector2(-2f, -3f),
            new Vector2(0f, -2f),
            new Vector2(2f, -3f),
            new Vector2(4f, -2f)
        };

        foreach (Vector2 position in coinPositions)
        {
            GameObject coin =
                CreateSprite(
                    "Coin",
                    position,
                    Color.yellow,
                    0.35f
                );

            CircleCollider2D collider =
                coin.AddComponent<CircleCollider2D>();

            collider.isTrigger = true;

            coin.AddComponent<Coin>();
        }

        // Enemy
        GameObject enemy =
            CreateSprite(
                "Enemy",
                new Vector2(0f, 4f),
                Color.red,
                0.65f
            );

        enemy.AddComponent<BoxCollider2D>();
        enemy.AddComponent<Enemy>();
    }

    private static GameObject CreateSprite(
        string objectName,
        Vector2 position,
        Color color,
        float size)
    {
        GameObject obj =
            new GameObject(objectName);

        obj.transform.position =
            new Vector3(
                position.x,
                position.y,
                0f
            );

        obj.transform.localScale =
            new Vector3(size, size, 1f);

        SpriteRenderer renderer =
            obj.AddComponent<SpriteRenderer>();

        renderer.sprite =
            CreateSquareSprite();

        renderer.color = color;

        return obj;
    }

    private static Sprite CreateSquareSprite()
    {
        Texture2D texture =
            new Texture2D(
                32,
                32,
                TextureFormat.RGBA32,
                false
            );

        texture.filterMode =
            FilterMode.Point;

        Color[] pixels =
            new Color[32 * 32];

        for (int i = 0; i < pixels.Length; i++)
            pixels[i] = Color.white;

        texture.SetPixels(pixels);
        texture.Apply();

        return Sprite.Create(
            texture,
            new Rect(0, 0, 32, 32),
            new Vector2(0.5f, 0.5f),
            32f
        );
    }
}
```
### Output:
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9f5955cd-d8a2-4b33-bd98-3fef6e8c77b5" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/169494ab-4e8e-42c0-a2c9-7b23e551d5e0" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/82ec53bb-b65f-4c9e-9419-23436aca96fb" />

### RESULT:
Thus the game was developed using Unity and adopted Rule-Based / Reactive AI technology.
