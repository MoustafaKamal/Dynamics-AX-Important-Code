# Dynamics-AX-Important-Code
Forms Development
First you create your query , tmp table , and start create your contract and controller and DP class 
first contract : 

it is like any contract class for any report:

  //  SRSReportParameterAttribute(classStr(SAED_GenerateLettersContract))
[DataContractAttribute]


class Saed_MKA_FreezingContract
{
    HcmWorkerRecId   worker;

}

[
DataMemberAttribute("worker"),
SysOperationLabelAttribute(literalStr("Worker"))
]
public HcmWorkerRecId parmWorker(HcmWorkerRecId _worker  = worker)
{
   worker = _worker;
   return worker;
}

then controller Class : 

 class Saed_MKA_FreezingBankController extends SrsReportRunController
{
 Saed_MKA_FreezingContract                   Contract; // your contract Class
 HcmWorkerBankAccount                     HcmWorkerBankAccount; //  Variable of same type of contract parametter 
}

protected Saed_MKA_FreezingContract parmContract()
{
    if(!contract)
    {
        contract = this.parmReportContract().parmRdpContract() as Saed_MKA_FreezingContract;
    }
    return contract;
}

protected void prePromptModifyContract()
{


    super();
    contract = this.parmReportContract().parmRdpContract() as Saed_MKA_FreezingContract;
    HcmWorkerBankAccount = args.record();  // Get the value from current record


    contract.parmWorker(HcmWorkerBankAccount.Worker); // pass value to contract
    //info(strFmt("%1  Controller",HcmWorker::find(HcmWorkerBankAccount.Worker).name()));

}

public static  void main(Args  _args)
{
   Saed_MKA_FreezingBankController                   controller;
   Saed_MKA_FreezingContract                         contractlocal;
   Object                                            caller;
   HcmWorkerBankAccount                              HcmWorkerBankAccount;

    controller= new Saed_MKA_FreezingBankController();
    controller.parmShowDialog(false);


    controller.parmReportName(ssrsReportStr(Saed_MKA_FreezingReport,PrecisionDesign1)); // your report with design you will work on 
    controller.parmArgs(_args);

    controller.startOperation();


}

Dp Class : 

[
    SRSReportQueryAttribute(querystr(Saed_MKA_FreezingQuery)),
    SRSReportParameterAttribute(classStr(Saed_MKA_FreezingContract))
]
class Saed_MKA_FreezingBankDP extends SrsReportDataProviderPreProcessTempDB
{
    Saed_MKA_FreezingTmp    Reportline;


}

[SRSReportDataSetAttribute(tableStr(Saed_MKA_FreezingTmp))]
public Saed_MKA_FreezingTmp  reportline()
{

select reportline ;
return reportline ;

}
    process Report Method :


    Saed_MKA_FreezingContract                   contract ;

    contract = this.parmDataContract() as Saed_MKA_FreezingContract;
    query = this.parmQuery();

    companyinfo   = companyInfo::find();

    ParmWorker = contract.parmWorker();

    queryBuildDataSource    = query.dataSourceTable(tablenum(hcmworkerbankaccount));
    queryBuildRange         = SysQuery::findOrCreateRange(queryBuildDataSource,fieldnum(hcmworkerbankaccount, Worker));
    queryBuildRange.value(Sysquery::value(Parmworker));

    queryRun = new QueryRun(query);

    while(queryRun.next())

            {
                hcmworkerbankaccount     = QueryRun.get(tableNum(hcmworkerbankaccount));
               // VendPackingSlipTrans    = QueryRun.get(tableNum(VendPackingSlipTrans));
               
               //write your logic here
               
//-- if you want to make validation to your report data :

before reportline.insert() 

write this code 
this.WorkerValidation(reportline.WorkerName,reportline.JobName,reportline.BankName,reportline.Position,reportline.IbanNumber,reportline.IqamaNumber);

and this method is created in the dp class and write your validation method with your parametters you want : 

private void WorkerValidation(str 100 WorkerName , str 100   JobName , str 100  BankName , str 100  Position  , str 100   Iban   , str 100 IqamaNumber  )
{
    str                            errorMsg;
    Description                    JobTitle;
 // Name                           WorkerName,ArabicName,Nationality,IqamaNumber,passportNumber;
    AmountMST                      totalSalary;
    SAED_WorkerCurrentProject      _SAED_WorkerCurrentProject;
    TransDate                       passportExpDate;


        if(!WorkerName)
    {

       errorMsg += "Arabic Worker Description is missing \n";

    }
        if(!JobName)
    {
        errorMsg += "Arabic Job Description is missing \n";
    }

        if(!BankName)
    {

        errorMsg += "Arabic Bank Description is missing \n";

    }

         if(!Position)
    {

        errorMsg += "Job position is missing \n";

    }

         if(!Iban)
    {
        errorMsg += "Iban Number is missing \n";
    }

         if(!IqamaNumber)
    {
        errorMsg += "Iqama Number is missing \n";

    }
    if(!WorkerName || !JobName || !BankName  || !Position   || !Iban  || !IqamaNumber)
    {
        throw error("Letter can not be generated please check below :- \n" + errorMsg);
    }

}



