# Dynamics-AX-Important-Code
Forms Development

//--go to class and create a method and put this code in it   

public str ValidateDateFormate(str 10 _splitString)

{



List strlist=new List(Types::String);

ListIterator    iterator;

str 20 _Value , result;

int  valueint , counter ;

boolean _NotFound=boolean::true;

;

strlist=strSplit(_splitString,"/");

iterator = new ListIterator(strlist);

Counter   = 1;

while(iterator.more())

{

_Value =iterator.value();
 Valueint = str2int(_value);

if (valueint >30 && Counter == 1)

{

    result = "Days";

}

if (valueint >12 && counter == 2 )

{

result = "Months";

}


iterator.next();
    Counter ++;

}

return result;

}

// then go to table and in methods overwirte aosValidateInsert method wite this code 

public boolean aosValidateInsert()
{
    boolean ret;
    date        startdate , enddate;
    SAED_Functions          SAED_Functions; // Class that contain common method used in tables and others 
    str 20                  Result , resultstart , resultend;

    ;
    SAED_Functions = new SAED_Functions();
      ret = super();
    if(this.HijriIssueDate && this.HijriEndDate && this.HijriStartDate)
    {

    Result      = SAED_Functions.ValidateDateFormate(this.HijriIssueDate);
    resultstart = SAED_Functions.ValidateDateFormate(this.HijriStartDate);
    resultend   = SAED_Functions.ValidateDateFormate(this.HijriEndDate);

    if(Result == "Days")
    {
      ret=  checkFailed("Check Formate");
    }

        else If( Result == "Months")
        {
            ret=  checkFailed("Check Formate");
        }

            if(resultstart == "Days")
            {
            ret=  checkFailed("Check Formate");
            }

                else If( Resultstart == "Months")
                {
                ret=  checkFailed("Check Formate");
                }

            if(Resultend == "Days")
                {
                    ret=  checkFailed("Check Formate");
                }

                    else If( Resultend == "Months")
                    {
                        ret=  checkFailed("Check Formate");
                    }

    startdate = SAED_Functions.ConvertToGregorianDate(this.HijriIssueDate);
    enddate   = SAED_Functions.ConvertToGregorianDate(this.HijriEndDate);

    if(startdate>enddate)
    {

        ret=  checkFailed("Issue date should be less than End date");
       // super();
    }

    }

    return ret;
}
