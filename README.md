using UnityEngine;
using TMPro;
public class Building : MonoBehaviour
{
    [SerializeField] private GameObject[] _GameBulid; 

    [SerializeField] private GameObject _Bulider;

    [SerializeField] private GameObject GameBuliderUI;

    [SerializeField] private GameObject AdjustingObjects = null;//调整物体用的

    [SerializeField] private GameObject BuliderUI;

    [SerializeField] private TMP_InputField X;

    [SerializeField] private TMP_InputField Y;

    [SerializeField] private TMP_InputField Z;

    [SerializeField] private TMP_Text XRotate;

    [SerializeField] private TMP_Text YRotate;

    [SerializeField] private TMP_Text ZRotate;

    [SerializeField] private float Speed;

    public int BulidingInxder;

    private bool IsBuldingMode = false;

    private RaycastHit hit;

    private Ray ray;

    void Update()
    {
        RayHit();
        IsBulidingMode();
        Mode();

        if (IsBuldingMode)
        {
            Buliding();
        }
    }
    private void Mode()
    {
        if (Input.GetKeyDown(KeyCode.Delete))
            if (Physics.Raycast(ray, out hit, 100f))
               if(hit.transform.tag=="Room")
                Destroy(hit.transform.gameObject);
            
     
        if (Input.GetMouseButtonDown(0))
        {
            if (IsBuldingMode == false)
            {
                if (Physics.Raycast(ray, out hit, 100f))
                {
                    if (hit.transform.tag == "Room")
                    {
                        AdjustingObjects = hit.transform.gameObject;
                        GameBuliderUI.SetActive(true);
                    }
                }
            }
    
        }
        if (AdjustingObjects != null)
        {
            Vector3 XYZ = AdjustingObjects.transform.eulerAngles;//创建一个向量，向量的值是AdjustingObjects
            XRotate.text = XYZ.x.ToString();
            YRotate.text = XYZ.y.ToString();
            ZRotate.text = XYZ.z.ToString();
            if (Input.GetKeyDown(KeyCode.Space))
            {          
              
                float xRotation = float.Parse(X.text);//把字符串转为字符点
                float yRotation = float.Parse(Y.text);//30
                float zRotation = float.Parse(Z.text);

                AdjustingObjects.transform.rotation = Quaternion.Euler(xRotation, yRotation, zRotation);
            }
          
            if (Input.GetMouseButton(1))
            {
                AdjustingObjects.transform.Rotate(Vector3.down * Speed * Time.deltaTime) ; 
            }
        }
    }
    private void IsBulidingMode()
    {
        if (Input.GetKeyDown(KeyCode.V) && IsBuldingMode == false)
        {
            IsBuldingMode = true;
            _Bulider.SetActive(true);
            BuliderUI.SetActive(true);
            GameBuliderUI.SetActive(false);
            AdjustingObjects = null;          
        }
        else if (Input.GetKeyDown(KeyCode.V) && IsBuldingMode == true)
        {
            IsBuldingMode = false;
            _Bulider.SetActive(false);
            BuliderUI.SetActive(false);
        }
    }
    private void RayHit()
    {
        Vector3 mousePosition = Input.mousePosition;//鼠标向量
        ray = Camera.main.ScreenPointToRay(mousePosition);//从鼠标的位置发射射线
    }
    private void Buliding()
    {
        if (Physics.Raycast(ray, out hit, 100f))
        {

            UpdateBuildingPosition(hit.point);

            if (Input.GetMouseButtonDown(0)&&hit.transform.tag!="Room")
            {
                PlaceBuilding(hit.point);//position
                _Bulider.transform.rotation = Quaternion.Euler(0, 0, 0);//归位


            }
        }
        if (Input.GetMouseButton(1))
        {
            _Bulider.transform.Rotate(Vector3.down * Speed * Time.deltaTime);
        }
    }
    private void UpdateBuildingPosition(Vector3 position)
    {
        position.y += _GameBulid[BulidingInxder].transform.localScale.y / 2;
        _Bulider.transform.position = position; 
    }

    private void PlaceBuilding(Vector3 position)//需要一个参数
    {
        position.y += _GameBulid[BulidingInxder].transform.localScale.y / 2; //让Room在地面上
        Instantiate(_GameBulid[BulidingInxder], position, _Bulider.transform.rotation);
    }
}
