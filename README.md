# Encryption-and-Decryption-2
C# code for encryption and decryption for windows form application 
public string Encrypt(string toEncrypt, bool useHashing)
        {
            toEncryptArray = UTF8Encoding.UTF8.GetBytes(toEncrypt);

            System.Configuration.AppSettingsReader settingsReader = new System.Configuration.AppSettingsReader();
            // Get the key from config file

            //string key = (string)settingsReader.GetValue("SecurityKey", typeof(String));
            //string key = UTF8Encoding.UTF8.GetString(keyArray);
            ////System.Windows.Forms.MessageBox.Show(key);
            ////If hashing use get hashcode regards to your key

            //keyArray = UTF8Encoding.UTF8.GetBytes(key);

            TripleDESCryptoServiceProvider tdes = new TripleDESCryptoServiceProvider();
            //set the secret key for the tripleDES algorithm
            try
            {
                tdes.Key = keyArray;
                //mode of operation. there are other 4 modes. We choose ECB(Electronic code Book)
                tdes.Mode = CipherMode.ECB;
                //padding mode(if any extra byte added)
                tdes.Padding = PaddingMode.Zeros;
                iv = tdes.IV;
                ICryptoTransform cTransform = tdes.CreateEncryptor();
                //transform the specified region of bytes array to resultArray
                byte[] resultArray = cTransform.TransformFinalBlock(toEncryptArray, 0, toEncryptArray.Length);
                //Release resources held by TripleDes Encryptor
                tdes.Clear();

                //Return the encrypted data into unreadable string format
                return Convert.ToBase64String(resultArray, 0, resultArray.Length);
            }
            catch (Exception  ae)
            {
                MessageBox.Show(ae.Message);
                return ae.Message;
            }
            

            }
        

        public string Decrypt(string cipherString, bool useHashing)
        {
            //get the byte code of the string

            byte[] toEncryptArray = UTF8Encoding.UTF8.GetBytes(cipherString);

            System.Configuration.AppSettingsReader settingsReader = new System.Configuration.AppSettingsReader();
            //Get your key from config file to open the lock!
            //string key = (string)settingsReader.GetValue("SecurityKey", typeof(String));
            string key = UTF8Encoding.UTF8.GetString(keyArray);
            if (useHashing)
            {
                //if hashing was used get the hash code with regards to your key
                MD5CryptoServiceProvider hashmd5 = new MD5CryptoServiceProvider();
                keyArray = hashmd5.ComputeHash(UTF8Encoding.UTF8.GetBytes(key));
                //release any resource held by the MD5CryptoServiceProvider

                hashmd5.Clear();
            }
            else
            {
                //if hashing was not implemented get the byte code of the key
                keyArray = UTF8Encoding.UTF8.GetBytes(key);
            }

            TripleDESCryptoServiceProvider tdes = new TripleDESCryptoServiceProvider();
            //set the secret key for the tripleDES algorithm
            tdes.Key = keyArray;
            tdes.IV=iv;
            //mode of operation. there are other 4 modes.
            //We choose ECB(Electronic code Book)

            tdes.Mode = CipherMode.ECB;
            //padding mode(if any extra byte added)
            tdes.Padding = PaddingMode.Zeros;

            ICryptoTransform cTransform = tdes.CreateDecryptor();
            byte[] resultArray = cTransform.TransformFinalBlock(toEncryptArray, 0, toEncryptArray.Length);
            //Release resources held by TripleDes Encryptor

            //string s=resultArray.ToString("X2");
                 
            tdes.Clear();
            //return the Clear decrypted TEXT
            return UTF8Encoding.UTF8.GetString(resultArray);
        }

        private void button2_Click(object sender, EventArgs e)
        {
            textBox2.Text = Encrypt(textBox2.Text, false);
        }

        private void button3_Click(object sender, EventArgs e)
        {
            textBox2.Text = Decrypt(textBox2.Text, false);
        }
    }
}
