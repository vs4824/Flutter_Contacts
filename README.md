# Flutter Contacts

Flutter plugin to read, create, update, delete and observe native contacts on Android and iOS, with group support, vCard support, and contact permission handling.

For a minimalistic example, take a look at example/. You can write a full-fledged contacts app with it – see example_full/ to see how.

## Quick start 

   ```
   // See installation notes below regarding AndroidManifest.xml and Info.plist
import 'package:flutter_contacts/flutter_contacts.dart';

// Request contact permission
if (await FlutterContacts.requestPermission()) {
  // Get all contacts (lightly fetched)
  List<Contact> contacts = await FlutterContacts.getContacts();

  // Get all contacts (fully fetched)
  contacts = await FlutterContacts.getContacts(
      withProperties: true, withPhoto: true);

  // Get contact with specific ID (fully fetched)
  Contact contact = await FlutterContacts.getContact(contacts.first.id);

  // Insert new contact
  final newContact = Contact()
    ..name.first = 'John'
    ..name.last = 'Smith'
    ..phones = [Phone('555-123-4567')];
  await newContact.insert();

  // Update contact
  contact.name.first = 'Bob';
  await contact.update();

  // Delete contact
  await contact.delete();

  // Open external contact app to view/edit/pick/insert contacts.
  await FlutterContacts.openExternalView(contact.id);
  await FlutterContacts.openExternalEdit(contact.id);
  final contact = await FlutterContacts.openExternalPick();
  final contact = await FlutterContacts.openExternalInsert();

  // Listen to contact database changes
  FlutterContacts.addListener(() => print('Contact DB changed'));

  // Create a new group (iOS) / label (Android).
  await FlutterContacts.insertGroup(Group('', 'Coworkers'));

  // Export contact to vCard
  String vCard = contact.toVCard();

  // Import contact from vCard
  contact = Contact.fromVCard('BEGIN:VCARD\n'
      'VERSION:3.0\n'
      'N:;Joe;;;\n'
      'TEL;TYPE=HOME:123456\n'
      'END:VCARD');
}
   ```

## Simplified contact model

   ```
   class Contact {
    String id;
    String displayName;
    Uint8List? photo;
    Uint8List? thumbnail;
    Name name;
    List<Phone> phones;
    List<Email> emails;
    List<Address> addresses;
    List<Organization> organizations;
    List<Website> websites;
    List<SocialMedia> socialMedias;
    List<Event> events;
    List<Note> notes;
    List<Group> groups;
}
class Name { String first; String last; }
class Phone { String number; PhoneLabel label; }
class Email { String address; EmailLabel label; }
class Address { String address; AddressLabel label; }
class Organization { String company; String title; }
class Website { String url; WebsiteLabel label; }
class SocialMedia { String userName; SocialMediaLabel label; }
class Event { int? year; int month; int day; EventLabel label; }
class Note { String note; }
class Group { String id; String name; }
   ```

## Installation

1. Add the following key/value pair to your app's Info.plist (for iOS):

   ```
   <plist version="1.0">
   <dict>
   ...
   <key>NSContactsUsageDescription</key>
   <string>Reason we need access to the contact list</string>
   </dict>
   </plist>
   ```
   
2. Add the following <uses-permissions> tags to your app's AndroidManifest.xml (for Android):

   ```
   <manifest xmlns:android="http://schemas.android.com/apk/res/android" ...>
    <uses-permission android:name="android.permission.READ_CONTACTS"/>
    <uses-permission android:name="android.permission.WRITE_CONTACTS"/>
    <application ...>
    ...
   ```
