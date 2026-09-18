# Assign Bridge roles to users

Bridge access is based on assigned roles. Only an Admin Console administrator for your organization can assign roles. Granting a user access is a two-step process:

1. Ensure that the user is part of your organization. Existing users who are already part of the organization only need a role assignment. Proceed directly to Assign a role. New users must first be added to the organization.
2. Assign a Bridge role to the user. The assigned role determines the user's level of access in the Bridge.

## Bridge roles

| Role                      | Access                                                                          |
|---------------------------|----------------------------------------------------------------------------------|
| **Bridge Admin**         | Full read-write access to the Bridge.                                            |
| **Bridge Viewer**        | Read-only access to the Bridge.                                                   |
| **Marketplace Assistant** | Access to Marketplace Assistant within the Bridge. See [Marketplace Assistant](./marketplace-assistant/index.md). |

Only users assigned the **Bridge Admin** or **Bridge Viewer** role can sign in to the Bridge. These roles are managed by Adobe and cannot be deleted by administrators.

The **Marketplace Assistant** role is assigned in addition to Bridge Admin or Bridge Viewer. It does not grant sign-in access to the Bridge on its own.

## Make user part of the organization

1. Sign in to [Adobe Admin Console](https://adminconsole.adobe.com/) using the admin account provided for your distribution.

![alt text](image-11.png)

2. From the Users tab, select **Add users**.
3. In the **Add users to your team** dialog box, enter the details of the user to be added and select **Save**.

![alt text](image-12.png)

## Assign a role

1. Sign in to [adminconsole.adobe.com](https://adminconsole.adobe.com) using the administrator account provided for your distribution, and select the correct account or organization.

2. From the top navigation bar, select **Users**.

   ![Marketplace Assistant role listed in the Admin Console Roles page](<marketplace-assistant/Screenshot 2026-08-28 143521.png>)

3. Select the role that you want to assign to the user.

   ![alt text](image-14.png)

   You can assign the **Bridge Admin**, **Bridge Viewer**, or **Marketplace Assistant** role.

4. Select **Add users**. The **Add users to this role** dialog box appears.

   ![alt text](image-15.png)

5. Select users who require the role and then select **Save**. The selected users can now access the Bridge according to the permissions granted by their assigned role.

   ![alt text](image-12.png)

Repeat this procedure whenever you need to add users or modify existing role assignments.

## Next step

- [Sign in and select your distribution](./sign-in.md)
