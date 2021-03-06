Security Architecture
---------------------

Users
-----

Objects representing users may be created in Principia
User Folder objects. User objects maintain the information
used to authenticate users, and allow roles to be associated
with a user.



Permissions
-----------

A "permission" is the smallest unit of access to an object,
roughly equivalent to the atomic permissions seen in NT:
R (Read), W(Write), X(Execute), etc. In Principia, a permission
usually describes a fine-grained logical operation on an object,
such as "View Management Screens", "Add Properties", etc.

Different types of objects will define different permissions
as appropriate for the object.



Types of access
---------------

A "type of access" is a named grouping of 0 or more of the
permissions defined by an object. All objects have one predefined
type of access called Full Access (all permissions defined by that 
object). A user who has the special role "Manager" always has Full 
Access to all objects at or below the level in the object hierarchy 
at which the user is defined.

New types of access may be defined as combinations of the
various permissions defined by a given object. These new
types of access may be defined by the programmer, or by
users at runtime. 




Roles
-----

A role is a name that ties users (authentication of identity)
to permissions (authorization for that identity) in the system.
Roles may be defined in any Folder (or Folderish) object in the
system. Sub folders can make use of roles defined higher in the
hierarchy. These roles can be assigned to users. All users, 
including non-authenticated users have the built-in role of 
"Anonymous". 

Principia objects allow the association of defined roles 
with a single "type of access" each, in the context of that 
object. A single role is associated with one and only one 
type of access in the context of a given object.



Examples
--------

  User                        Object1

  o has the role "RoleA"      o has given "RoleA" Full Access

  Result: the user has Full Access to Object1.



  User                        Object2
  o has the role "RoleA"      o has given "RoleB" Full Access

                              o has given the role "RoleA" View Access,
                                a custom type of access that allows only
                                viewing of the object.

  Result: the user has only View Access.



Notes
-----

All objects define a permission called "Default permission". If this
permission is given to a role, users with that role will be able to
access subobjects which do not explicitly restrict access.



Technical
---------

Objects define their permissions as logical operations.
Programmers have to determine the appropriate operations
for their object type, and provide a mapping of permission
name to attribute names. It is important to note that permissions
cannot overlap - none of the attributes named in a permission
can occur in any of the other permissions. The following are
proposed permissions for some current principia objects:


Folder
  o View management screens
  o Change permissions
  o Undo changes
  o Add objects
  o Delete objects
  o Add properties
  o Change properties
  o Delete properties
  o Default permission

Confera Topic
  o View management screens
  o Change permissions
  o Undo changes
  o Add objects
  o Delete objects
  o Add properties
  o Change properties
  o Delete properties
  o Default permission
  o Change Configuration
  o Add Messages
  o Change Messages
  o Delete Messages

Tabula Collection
  o View management screens
  o Change permissions
  o Undo changes
  o Add objects
  o Delete objects
  o Add properties
  o Change properties
  o Delete properties
  o Default permission
  o Change schema
  o Upload data
  o Add computed fields
  o Change computed fields
  o Delete computed fields

Document/Image/File
  o View management screens
  o Change permissions
  o Change/upload data
  o View

Session
  o View management screens
  o Change permissions
  o Change session config
  o Join/leave session
  o Save/discard session

Mail Host
  o View management screens
  o Change permissions
  o Change configuration


To support the architecture, developers must derive an
object from the AccessControl.rolemanager.RoleManager mixin class,
and define in their class an __ac_permissions__ attribute.

This should be a tuple of tuples, where each tuple represents
a permission and contains a string permission name as its first
element and a list of attribute names as its second element.

Example:

    __ac_permissions__=(

    ('View management screens',
     ['manage','manage_menu','manage_main','manage_copyright',
      'manage_tabs','manage_propertiesForm','manage_UndoForm']),
    ('Undo changes',       ['manage_undo_transactions']),
    ('Change permissions', ['manage_access']),
    ('Add objects',        ['manage_addObject']),
    ('Delete objects',     ['manage_delObjects']),
    ('Add properties',     ['manage_addProperty']),
    ('Change properties',  ['manage_editProperties']),
    ('Delete properties',  ['manage_delProperties']),
    ('Default permission', ['']),
    )

The developer may also predefine useful types of access, by
specifying an __ac_types__ attribute. This should be a tuple of 
tuples, where each tuple represents a type of access and contains 
a string name as its first element and a list of permission names 
as its second element.

By default, only "Full Access" is defined (by the RoleManager mixin).
If you wish to override __ac_types__ to provide convenient types of
access, you must always be sure to define "Full Access" as containing 
the names of all possible permissions for your object.

Example:

    __ac_types__=(

    ('Full Access', tuple(map(lambda x: x[0], __ac_permissions__))),
    ('Change', ['Add Objects', 'Add Properties', 'Change Properties']),

    )

Developers may also provide pre-defined role names that are
not deletable via the interface by specifying an __ac_roles__
attribute. This is probably not something we'll ever use under
the new architecture, but it's there if you need it.

Example:

    __ac_roles__=('Manager', 'Anonymous')
