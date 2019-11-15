Collaborations
==============

Collaborations are used to share folders and files between users or groups. They also define what permissions a user
has for a folder or file.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Get Collaboration](#get-collaboration)
- [Add Collaboration](#add-collaboration)
- [Update Collaboration](#update-collaboration)
- [Delete Collaboration](#delete-collaboration)
- [Get Pending Collaborations](#get-pending-collaborations)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Get Collaboration
-----------------

To retrieve a Collaboration record from the API, call
[`client.collaborations.get(collaborationId:fields:completion:)`][get-collaboration]
with the ID of the collaboration.

```swift
client.collaborations.get(collaborationId: "12345") { (result: Result<Collaboration, BoxError>) in
    guard case let .success(collaboration) = result else {
        print("Error retrieving collaboration")
        return
    }

    let collaborator: String
    switch collaboration.accessibleBy.collaboratorValue {
    case let .user(user):
        collaborator = "user \(user.name)"
    case let .group(group):
        collaborator = "group \(group.name)"
    }

    let itemName: String
    switch collaboration.item.itemValue {
    case let .file(file):
        itemName = file.name
    case let .folder(folder):
        itemName = folder.name
    case let .webLink(webLink):
        itemName = webLink.name
    }

    print("Collaboration \(collaboration.id) gives \(collaborator) access to \(itemName)")
}
```

[get-collaboration]: http://opensource.box.com/box-ios-sdk/Classes/CollaborationsModule.html#/s:6BoxSDK20CollaborationsModuleC16get5collaborationId6fields10completionySS_SaySSGSgys6ResultOyAA0F0CAA0A5ErrorOGctF

Add Collaboration
-----------------

To add a collaborator to an item, call
[`client.collaborations.create(itemType:itemId:role:accessibleBy:accessibleByType:canViewPath:fields:notify:completion:)`][create-collaboration]
with the type and ID of the item, as well as the type and ID of the collaborator — a user or a group.  A `role` for the
collaborator must be specified, which will determine the permissions the collaborator receives on the item.

```swift
client.collaborations.create(
    itemType: "folder",
    itemId: "22222",
    role: .editor,
    accessibleBy: "33333",
    accessibleByType: "user"
) { (result: Result<Collaboration, BoxError>) in
    guard case let .success(collaboration) = result else {
        print("Error creating collaboration")
        return
    }

    print("Collaboration successfully created")
}
```

[create-collaboration]: http://opensource.box.com/box-ios-sdk/Classes/CollaborationsModule.html#/s:6BoxSDK20CollaborationsModuleC19create8itemType0G2Id4role12accessibleBy0klH011canViewPath6fields6notify10completionySS_SSAA0F4RoleOSSAA010AccessibleL0OSbSgSaySSGSgARys6ResultOyAA0F0CAA0A5ErrorOGctF

Update Collaboration
--------------------

To update a collaboration record, call
[`client.users.update(collaborationId:role:status:canViewPath:fields:completion:)`][update-collaboration]
with the ID of the collaboration to update and the properties to update, including at least the `role`.

```swift
client.collaborations.update(collaborationId: "12345", role: .viewer) { (result: Result<Collaboration, BoxError>) in
    guard case let .success(collaboration) = result else {
        print("Error updating collaboration")
        return
    }

    print("Updated collaboration")
}
```

[update-collaboration]: http://opensource.box.com/box-ios-sdk/Classes/CollaborationsModule.html#/s:6BoxSDK20CollaborationsModuleC19update15collaborationId4role6status11canViewPath6fields10completionySS_AA0F4RoleOAA0F6StatusOSgSbSgSaySSGSgys6ResultOyAA0F0CAA0A5ErrorOGctF

Delete Collaboration
--------------------

To delete a collaboration, removing the collaborator's access to the relevant item, call
[`client.collaborations.delete(collaborationId:completion:)`][delete-collaboration]
with the ID of the collaboration to delete.

```swift
client.collaborations.delete(collaborationId: "12345") { (result: Result<Void, BoxError>) in
    guard case .success = result else {
        print("Error deleting collaboration")
        return
    }

    print("Collaboration deleted")
}
```

[delete-collaboration]: http://opensource.box.com/box-ios-sdk/Classes/CollaborationsModule.html#/s:6BoxSDK20CollaborationsModuleC19delete15collaborationId10completionySS_ys6ResultOyytAA0A5ErrorOGctF

Get Pending Collaborations
--------------------------

To retrieve a list of the pending collaborations requiring the user to accept or reject them, call
[`client.collaborations.listPendingForEnterprise(offset:limit:fields:)`][get-pending-collaborations].
The method returns an iterator that can be used to page through the collection of pending collaborations.

```swift
let pendingCollabIterator = client.collaborations.listPendingForEnterprise()
pendingCollabIterator.nextItems { (result: Result<[Collaboration], BoxError>) in
    guard case let .success(collaborations) = result else {
        print("Error retrieving next page of collaborations")
        return
    }

    print("User has \(collaborations.count) pending collaborations")
}
```

[get-pending-collaborations]: http://opensource.box.com/box-ios-sdk/Classes/CollaborationsModule.html#/s:6BoxSDK20CollaborationsModuleC010listPendingForEnterpriseC06offset5limit6fieldsAA18PaginationIteratorCyAA13CollaborationCGSiSg_AMSaySSGSgtF