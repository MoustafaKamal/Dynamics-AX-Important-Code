# Dynamics-AX-Important-Code
Forms Development


public date ConvertToGregorianDate(String15 hijriDateStr )
{

    //String15 hijriDateStr = "17/05/1438" ;

    System.Globalization.CultureInfo arCul = new System.Globalization.CultureInfo("ar-SA");
    System.Globalization.CultureInfo enCul = new System.Globalization.CultureInfo("en-US");
    System.DateTime                  tempDateTime;
    str                              strTemp;
    System.String[]                  arr;
    date                             grDate;

;

    //all expected dates formats
    arr = new System.String[18]();
    arr.SetValue("dd M yyyy",  0);
    arr.SetValue("yyyy/MM/dd",  1);
    arr.SetValue("yyyy/M/d",    2);
    arr.SetValue("d/M/yyyy",    3);
    arr.SetValue("dd/MM/yyyy",  4);
    arr.SetValue("yyyy-MM-dd",  5);
    arr.SetValue("d/MM/yyyy",   6);
    arr.SetValue("dd/M/yyyy",   7);
    arr.SetValue("yyyy-M-d",    8);
    arr.SetValue("dd-MM-yyyy",  9);
    arr.SetValue("yyyy MM dd",  10);
    arr.SetValue("d-M-yyyy",    11);
    arr.SetValue("d-MM-yyyy",   12);
    arr.SetValue("dd-M-yyyy",   13);
    arr.SetValue("d M yyyy",    14);
    arr.SetValue("dd MM yyyy",  15);
    arr.SetValue("yyyy M d",    16);
    arr.SetValue("d MM yyyy",   17);

    try
    {
        tempDateTime = System.DateTime::ParseExact(hijriDateStr, arr, arCul, System.Globalization.DateTimeStyles::AllowWhiteSpaces);
    }
    catch
    {
        error("Unexpected Hirji date format.");
      //  return dateNull();
    }
    strTemp = tempDateTime.ToString("dd/MM/yyyy");
    grDate = str2date(strTemp, 123);

   // info( strFmt("%1",grDate));
    return grDate;

}



//-----------------------------------------------------------------------------------------------------------------


server static date ConvertStrToDate(str strDate)
{
    DictTable                       _dictTable;
  
    Statement                       _statement;
    str                             _sql,strResultDate;
    ResultSet                       _resultSet;
    Enumerator                      enum;
    SqlStatementExecutePermission   _perm;
    date                            _dateTemp;

    LoginProperty                   LoginProperty;
    ODBCConnection                  ODBCConnection;
    SqlSystem                       SqlSystem;
    ;

    SqlSystem       =   new SqlSystem();
    LoginProperty   =   new LoginProperty();

    LoginProperty.setServer(SqlSystem.loginServer());
    LoginProperty.setDatabase(SqlSystem.loginDatabase());

    ODBCConnection  =   new ODBCConnection(LoginProperty);



    _Sql= "select convert(datetime,'" + strDate + "', 131)" ;
    try
    {
        //_connection = new Connection();

        //_sql = strfmt( _args.toString() );
        _perm = new SqlStatementExecutePermission(_sql);

        // Check for permission to use the _statement.
        _perm.assert();
        _statement = ODBCConnection.createStatement();
        _resultSet = _statement.executeQuery(_sql);

        // End the scope of the assert call.
        CodeAccessPermission::revertAssert();

        _resultSet.next();

        strResultDate = _resultSet.getString(1);
        strResultDate  = strLrTrim(strResultDate);
        enum = strSplit(strResultDate," ").getEnumerator();

        if(enum.moveNext())
        strResultDate = enum.current();
    }
    catch
    {
        error("error");
    }

    try
    {
        _dateTemp = any2date(strResultDate);
    }
    catch(Exception::Error)
    {
        error("error");
        return datenull();
    }

    return  any2date(strResultDate);
}


