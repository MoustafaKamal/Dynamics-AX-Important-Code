# Dynamics-AX-Important-Code
first Create Number Sequance From Organization Administration
then go to your table and put Recid Field from Number Sequence Table to your table 
and make a relation between Number Sequence Table And table. 

then into your table create this method : 

public NumberSeq initNumSeq()
{
    NumberSequenceReference NumberSequenceReference;
    NumberSeq num;
    Saed_MKA_FreezingTable_Setup Saed_MKA_FreezingTable_Setup; // if you make number sequance in anther table take a buffer here 


    if(this.Saed_MKA_FreezingNum=='') // your sequance number
    {
        Select * from Saed_MKA_FreezingTable_Setup;

        num = NumberSeq::newGetNumFromCode(NumberSequenceTable::find(Saed_MKA_FreezingTable_Setup.NumberSequenceTable).NumberSequence);
        this.Saed_MKA_FreezingNum = num.num();
        num.used(); // Mark the Number as Used
    }
    return num;
}

if you want to make the number sequance created once you open a form 

at form datasource overrite initvalue method and write this code :

//code start------------------------------
public void initValue()
{
    super();
    Saed_MKA_FreezingTable.Saed_MKA_FreezingNum='';
    Saed_MKA_FreezingTable.initNumSeq();
}
// code ended ----------------------------

once you open the form and press new it will automatically initiate the new sequance number. 
