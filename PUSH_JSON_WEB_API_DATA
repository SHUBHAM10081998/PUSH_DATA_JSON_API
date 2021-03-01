private void SEND_BIO_LIVE_DATA()
        {
            try
            {
                string strSMSTYPE = "[SENT_BIO_LIVE_DATA_TRANSFER]";//stored procedure
                SqlConnection.ClearPool(getconn());
                logPath = @"D:\BIO_LOG\EXE\Logs" + (DateTime.Now.ToString("dd/MM/yyyy", CultureInfo.InvariantCulture) + ".txt").Replace("/", "");
                if (serverStatus == 0)
                {
                    mystatus = strSMSTYPE + " Could not able to process the request server not started";
                    writeLogFile(logPath, "Could not able to process the request server not started");
                    return;
                }
                string strFLAG1 = "", strFLAG2 = "", strCALL_NUMBER = "", strCALL_ADDONS_URL = "";
                string strQuery = "", strcall_total_duration = "", strcall_start_time = "", strcall_end_time = "", strcall_cli = "";

                strQuery += "select DeviceLogId,cast(logdate as date) as att_date,userid as emp_code,(select EmployeeName  from eSSLSmartOffice.dbo.Employees e where e.EmployeeCode=d.userid) as emp_name,(select CompanyFName from eSSLSmartOffice..Companies  where CompanyId in(select top 1 companyid from eSSLSmartOffice.dbo.Employees e where e.EmployeeCode=d.userid )) as company,cast(logdate as time) as in_time  from Dummy_eSSLSmartOffice.dbo.DeviceLogs_2_2021 d where Issent='NO'";
                
                DataTable dt = new DataTable();
                SqlDataAdapter da = new SqlDataAdapter(strQuery, getconn());
                da.Fill(dt);
                for (int i = 0; i < dt.Rows.Count; i++)
                {
                    string Unique_DeviceLogId = "";
                    String dttdt = "";
                    string dttdt1 = "";
                    String EmpCd = "";
                    String EmpNm = "";
                    String Cmpny = "";
                    String Time = "";
                    dttdt = dt.Rows[i]["att_date"].ToString();
                    DateTime bio_date = Convert.ToDateTime(dttdt);
                    string  biodate;
                    Unique_DeviceLogId = dt.Rows[i]["DeviceLogId"].ToString();
                    biodate = bio_date.ToString("yyyy-MM-dd"); 
                    biodate = biodate.Substring(0, 10);
                    EmpCd = dt.Rows[i]["emp_code"].ToString();
                    EmpNm = dt.Rows[i]["emp_name"].ToString();
                    Cmpny = dt.Rows[i]["company"].ToString();
                    Time = dt.Rows[i]["in_time"].ToString();
                    try
                    {
                    
                            string url = "http://corereliable.com/Live_intime/live_attendance";
                            string stringResult = "";
                            String jsonString = @"{";
                            jsonString += @"    ""att_date"": """ + biodate + @""",";
                            jsonString += @"    ""emp_code"":  """ + EmpCd + @""",";
                            jsonString += @"    ""emp_name"": """ + EmpNm + @""",";
                            jsonString += @"    ""company"": """ + Cmpny + @""",";
                            jsonString += @"    ""in_time"": """ + Time + @"""";
                            jsonString += @"}";
                            System.Net.WebRequest req = System.Net.WebRequest.Create(url);
                            req.ContentType = "application/json; charset=utf-8";
                            req.Method = "POST";
                            byte[] bytes = System.Text.Encoding.ASCII.GetBytes(jsonString);
                            req.ContentLength = bytes.Length;
                            System.IO.Stream os = req.GetRequestStream();
                            os.Write(bytes, 0, bytes.Length);
                            os.Close();
                            System.Net.WebResponse resp = req.GetResponse();
                            System.IO.StreamReader sr = new System.IO.StreamReader(resp.GetResponseStream());
                            stringResult = sr.ReadToEnd().Trim();
                            SqlCommand cmd = new SqlCommand();
                            cmd.CommandType = CommandType.StoredProcedure;
                            cmd.CommandText = "USP_UPDATE_API_REPOSNE";
                            cmd.Parameters.AddWithValue("@att_date", biodate);
                            cmd.Parameters.AddWithValue("@emp_code", EmpCd);
                            cmd.Parameters.AddWithValue("@emp_name", EmpNm);
                            cmd.Parameters.AddWithValue("@company", Cmpny);
                            cmd.Parameters.AddWithValue("@in_time", Time);
                            cmd.Parameters.AddWithValue("@Unique_DeviceLogId", Unique_DeviceLogId);
                            cmd.Parameters.AddWithValue("@Response", stringResult);
                            cmd.Connection = getconn();
                            cmd.Connection.Close();
                            cmd.Connection.Open();
                            cmd.ExecuteNonQuery();
                            cmd.Connection.Close();
                            sr.Close();
                            mystatus += "sent";
                            writeLogFile(Form1.logPath, mystatus);
                            this.Invoke(this.updateStatusDelegate);
                            
                    }
                    catch (Exception ex)
                    {
                        mystatus = ex.Message;
                    }
                }
            }

            catch (Exception ex)
            {
                mystatus = ex.Message;
            }
            this.Invoke(this.updateStatusDelegate);
        }
