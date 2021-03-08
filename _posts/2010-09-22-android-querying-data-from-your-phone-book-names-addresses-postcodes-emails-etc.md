---
title: 'Android; Querying data from your phone book; names, addresses, postcodes, emails etc'
date: '2010-09-22T14:25:10+10:00'
permalink: /android-querying-data-from-your-phone-book-names-addresses-postcodes-emails-etc
author: 'James Elsey'
category:
    - Android
    - Java
tag: []

---
One of the more useful features of Android is the ability to obtain data from elements of Android itself, such as the phone book. Your application may require data from your phone itself, such as your contacts, and more specific items such as all your contacts email addresses, or postcodes. In this post I will help explain how you can obtain this data. There are some details on the Android documenation, but the examples are not particularly clear.

The easiest method for retreiving multiple contact data at a time is to first obtain all the unique contact IDs, and then use those in further loops to query for other data. I had researched multiple ways of obtaining this data, but most references that I could find on the web were refering to version 1.6 of Android, which uses the *People* class. The below version is fully updated for 2.2.

Contact IDs
-----------

The first task you need to do is to obtain the IDs for your contacts, please have a look at the following snippet, then refer to the description below to understand what is happening

```java
Cursor cursor = getContentResolver().query(
				ContactsContract.Contacts.CONTENT_URI, null, null, null, null);
if (cursor.getCount() > 0)
{
	while (cursor.moveToNext())
        {
	    // Pick out the ID, and the Display name of the
            // contact from the current row of the cursor
            String id = cursor.getString(cursor.getColumnIndex(BaseColumns._ID));
            String name = cursor.getString(cursor.getColumnIndex(
                                ContactsContract.Contacts.DISPLAY_NAME));
            // Do something with the values you have,
            // such as print them out or add to a list
            System.out.println("Current contact on this iteration is : " + name);

            // This is where we query for Emails, Addresses etc
            // Add snippets below into here, depending on what you need
	}
}
cursor.close();
```

The above code will query the CONTENT\_URI provider, this contains the contacts ID and Display name. We need to do this first so we can retain the contact ID and use it in where clauses when obtaining other data. I will explain more about how the querying works at a later date.

So, run the above and you will get the ID and name for each contact on iteration. So if you have 5 contacts in your phone book, it will loop through 5 times and print out each of their IDs and names. I’d suggest you do something more useful that just printing them out, add them into a collection so you can use this elsewhere

Contact Addresses, including postcodes
--------------------------------------

From the above code, we already have the contact Ids, so lets use those to pick out the addresses for contacts. Have a look at the following

```java
String addrWhere = ContactsContract.Data.CONTACT_ID
		+ " = ? AND " + ContactsContract.Data.MIMETYPE + " = ?";

// We already have the contact Id in id, so lets use it in the where clause
String[] addrWhereParams = new String[] {id,
		ContactsContract.CommonDataKinds.StructuredPostal.CONTENT_ITEM_TYPE };

// Run the query, using the where params
Cursor addrCur = getContentResolver().query(
						ContactsContract.Data.CONTENT_URI,
						null,
						addrWhere,
						addrWhereParams,
						null);
while (addrCur.moveToNext()) {
String postalCode = addrCur
	.getString(addrCur
	.getColumnIndex(ContactsContract.CommonDataKinds
					.StructuredPostal.POSTCODE));
String streetName = addrCur
	.getString(addrCur
	.getColumnIndex(ContactsContract.CommonDataKinds
					.StructuredPostal.STREET));
String cityName = addrCur
	.getString(addrCur
	.getColumnIndex(ContactsContract.CommonDataKinds
					.StructuredPostal.CITY));
System.out.println(&quot;Postcode is " + postalCode);
System.out.println(&quot;Street name is " + streetName);
System.out.println(&quot;City name is is " + cityName);
}
addrCur.close();
```

Using the contact ID we obtained earlier, we use this to pick out the addresses for that particular contact. I have only picked out the POSTCODE, STREET and CITY, but there are several others specified in the Android documenation

Contact Email addresses  
Contact Phone numbers  
Others