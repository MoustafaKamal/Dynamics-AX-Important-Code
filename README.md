Insert Data to table using class with parametters . 

firts you should create a class extends from runbasebatch :

// class declaration method

class Saed_MKA_CreateAbsence extends RunBaseBatch
{
    DialogField                         dlgFileNameOpen,dlgToDate;
    FileNameOpen                        fileName;
    TransDate                           fromDate,toDate;

    #define.CurrentVersion(1)
    #define.Version1(1)

    #localmacro.CurrentList
    fileName
    #endmacro
}


// 

public Object dialog()
{
    Object          mainGroup0, mainGroup1;
    DialogTabPage   tab0, tab1;


    DialogRunbase   dialog = super();

    mainGroup0 = dialog.mainFormGroup();
    dialog.mainFormGroup(mainGroup0);
    dialog.addGroup("Absence");
    dlgFileNameOpen   = dialog.addFieldValue(extendedTypeStr(TransDate),filename,"From date","");
    dlgToDate        =  dialog.addFieldValue(extendedTypeStr(TransDate),filename,"To date","");

    dialog.caption("Absence");
    mainGroup1 = dialog.mainFormGroup();
   // tab1 = dialog.addTabPage("@SYS14501");
    dialog.mainFormGroup(mainGroup1);


    dialog = this.dialogInternal(dialog);

    return dialog;
}

//

public boolean getFromDialog()
{
    filename  = dlgFileNameOpen.value();
    fromDate  = dlgFileNameOpen.value();
    toDate    = dlgToDate.value();
    return true;
}

public container pack()
{
    return [#CurrentVersion] + [#CurrentList];
}

//

public void run()
{
    Saed_MKA_Absence_Days           Saed_MKA_Absence_Days , Saed_MKA_Absence_Days1 ;
    //Saed_MKA_Absence_Days_Lines     Saed_MKA_Absence_Days_Lines;
    SAED_SwipeTrans                 SAED_SwipeTrans;
    int                             Counter;
    str 50                          PersonnelNumber , WorkerName , MonthName;
    TransDate                       TransDate;
    real                            Days;

    ;

    Counter = 1 ;

    while select forUpdate  SAED_SwipeTrans where
    SAED_SwipeTrans.Absence == NoYes::No &&
    SAED_SwipeTrans.TransDate >= FromDate && //Current Month dateStartMth(today())  dateEndMth
    SAED_SwipeTrans.TransDate <= toDate  // date parametters come from class 
    {
        if(SAED_SwipeTrans.status() == "Absent")
        {


                Days                  = Counter;
                PersonnelNumber       = SAED_SwipeTrans.PersonnelNumber;
                WorkerName            = HcmWorker::findByPersonnelNumber(PersonnelNumber).name();
                TransDate             = SAED_SwipeTrans.TransDate;
                MonthName             = mthName(str2int(strFmt("%1",mthOfYr(TransDate))));
                Counter++ ;
                select * from Saed_MKA_Absence_Days1 where
                Saed_MKA_Absence_Days1.PersonnelNumber == PersonnelNumber &&
                Saed_MKA_Absence_Days1.TransDate >=  FromDate &&
                Saed_MKA_Absence_Days1.TransDate <=  toDate;

                if(Saed_MKA_Absence_Days1)
            {

                select forUpdate Saed_MKA_Absence_Days where
                Saed_MKA_Absence_Days.PersonnelNumber == PersonnelNumber &&
                Saed_MKA_Absence_Days.TransDate >=  FromDate &&
                Saed_MKA_Absence_Days.TransDate <=  toDate;
                ttsBegin;
                    Saed_MKA_Absence_Days.Days            =  Saed_MKA_Absence_Days.Days + 1;
                    Saed_MKA_Absence_Days.update();
                //info(strFmt("%1   %2", Days, "updated"));
                ttsCommit;
            }
            else
            {
                    Saed_MKA_Absence_Days.TransDate       = TransDate;
                    Saed_MKA_Absence_Days.WorkerName      = WorkerName;
                    Saed_MKA_Absence_Days.PersonnelNumber = PersonnelNumber;
                    Saed_MKA_Absence_Days.MonthName       = MonthName;
                    Saed_MKA_Absence_Days.Days            = 1;
                    Saed_MKA_Absence_Days.insert();
            }
            //Counter++ ;
        }
            ttsBegin ;
                SAED_SwipeTrans.Absence = NoYes::Yes;
                SAED_SwipeTrans.update();
            ttsCommit;
    }
}

//

public boolean unpack(container packedClass)
{
    Version version = RunBase::getVersion(packedClass);
;
    switch (version)
    {
        case #CurrentVersion:
            [version, #CurrentList] = packedClass;
            break;
        default:
            return false;
    }

    return true;
}

//

static void main(Args   args)
{
    // Args args;
    FormRun                                 formRun;
    Saed_MKA_CreateAbsence                  import = New Saed_MKA_CreateAbsence();

    if (import.prompt())
    {
          import.run();
          args = new Args();
          args.name(formstr(Saed_MKA_AbsenceForm));
          formRun=new FormRun(args);
          formRun.run();
          formRun.wait();
    }

}

