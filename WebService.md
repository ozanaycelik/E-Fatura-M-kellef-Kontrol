using DevExpress.XtraEditors;
using GibList.GibElogoService;
using System;
using System.Collections;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace GibList
{

    public partial class Form1 : XtraForm
    {
        PostBoxServiceClient serviceClient = new PostBoxServiceClient("PostBoxServiceEndpoint1");

        public Form1()
        {
            InitializeComponent();
            txt_vknOrTckn.Properties.MaxLength = 11;

        }
        private void textBox1_KeyPress(object sender, KeyPressEventArgs e)
        {
            e.Handled = !char.IsDigit(e.KeyChar) && !char.IsControl(e.KeyChar);
        }


        private void simpleButton1_Click(object sender, EventArgs e)
        {

            gridControl1.DataSource = "";

            if (txt_vknOrTckn.Text.Length < 10)
            {
                XtraMessageBox.Show("Yanlış giriş yaptınız doğru bir değer giriniz!", "Bilgi", MessageBoxButtons.OK, MessageBoxIcon.Information);
            }
            else
            {
                LoginType login = new LoginType();
                    login.userName = "~";
                  login.passWord = "~";

                if (serviceClient.Login(login, out string sessionID))
                {
                    var vkn = string.Concat("VKN=", txt_vknOrTckn.Text);
                    var VKNdeger = new string[] {vkn };

                    var result = serviceClient.GetValidateGIBUser(sessionID, VKNdeger);
                    if (result.outputList[0].ToString()== "ISGIBUSER=0")
                    {
                        XtraMessageBox.Show("Girdiğiniz vkn veya tc no gib kullanıcısı değildir.", "Bilgi", MessageBoxButtons.OK, MessageBoxIcon.Information);
                    }
                    else
                    {
                        gridControl1.DataSource = result.outputList;
                    }
                   

                    serviceClient.Logout(sessionID);
                }
                else
                {
                    XtraMessageBox.Show("Login olunamadı.", "Bilgi", MessageBoxButtons.OK, MessageBoxIcon.Information);
                }


            }

        }
    }
}
