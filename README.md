# Dynamics-AX-Important-Code
Forms Development

You have to modify validateWrite() of table InventJournalTrans like Sohabib Seema supposed to.

Additionally you can modify the interface, so that the formcontrols are marked as mandatory. You can do this by overwriting the active-method of the dataSource InventJournalTrans like this:

public int active()

{

   int ret = super();

   inventJournalTrans_ds.object(fieldNum(inventJournalTrans, Qty)).mandatory(inventJournalTrans.inventJournalTable().JournalNameId == 'Mov-Out');

   inventJournalTrans_ds.object(fieldNum(inventJournalTrans, CostPrice)).mandatory(inventJournalTrans.inventJournalTable().JournalNameId == 'Mov-In');

   return ret;

}

But note that this modification only creates the "red line" in the form control, you have to check the values in the validateWrite() of InventjournalTrans.

public boolean validateWrite()

{

   boolean isValid;

   ...existing code in this method

   if(this.inventJournalTable().journalNameId == 'Mov-Out' && !this.Qty)

   {

       isValid = checkfailed('Field Quantity missing');

   }

   if(this.inventJournalTable().journalNameId == 'Mov-In' && !this.CostPrice)

   {

       isValid = checkfailed('Field Cost price missing');

   }

   return isValid;

}
