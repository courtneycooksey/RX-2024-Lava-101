# RX2024 Lava 101
[Slide Deck]()

[Takeaway]()

Below are the code snippets from the slides arranged by slide title. Github allows for an easy copy of each snippet to make it easier to follow along.

## Lava Syntax

```html
<p>
    Hello User!
</p>
```

```html
<p>
    Hello {{User}}!
</p>
```

```html
<p>
    Hello {{ CurrentPerson.NickName }}!
</p>
```

Depending on your version of Rock and block context `Person` could also be available.

## All Lava is defined by a set of matching symbols

```html
<p>
    Hello {{ CurrentPerson.NickName }}!
</p>
```

```html
{% assign x = 7 %}
<p>
    X is {{ x }}.
</p>
```

## Tags Can Be Inline Or Block

```liquid
{% assign myVariable = 'This is the value of my variable.' %}
```

```liquid
{% capture myVariable %}
    ...Some code...
{% endcapture %}
```

## The Assign Tag

```liquid
{% assign myVariable = 'The first value' %}
myVariable is {{ myVariable }}
<br/>
{% assign myVariable = 'The second value' %}
myVariable has been changed to {{ myVariable }}
```

## Types Of Values You Can Assign To Your Custom Variables

```liquid
{% assign myString = 'This is a string.' %}
```

```liquid
{% assign myNumber = 7 %}
```

```liquid
{% assign myBool = true %}
```

```liquid
{% assign myDate = '2024-01-01 06:00:00' | Date:'yyyy-MM-dd HH:mm:ss' %}
```

## The Capture Tag

```liquid
{% capture greeting %}Hello, {{CurrentPerson.NickName}}{% endcapture %}
{{ greeting }}
```

## If Is a Tag That Allows for Conditional Logic

```liquid
{% if CurrentPerson.LastName == 'Decker' %}
    ...This is only run when last name is Decker...
{% endif %}
```

## Using Comparison Operators

```liquid
{% if CurrentPerson.LastName == 'Decker' %}
    ...Prints when the logged in user's last name is Decker...
{% endif %}
{% if CurrentPerson.LastName != 'Decker' %}
    ...Prints when the logged in user's last name is anything except Decker...
{% endif %}

{% if CurrentPerson.LastName contains 'D' %}
    ...Prints when the logged in user's last name has a "D"...
{% endif %}

{% if CurrentPerson.Age >= 50 %}
    ...Prints when the logged in user's age is 50 or higher...
{% endif %}
```

## You Can Have Multiple Conditions

```liquid
{% if Person.LastName == 'Decker' and Person.FirstName == 'Ted' %}
```

```liquid
{% if Person.LastName == 'Decker' or Person.LastName == 'Marbles' %}
```

## Stacking Conditions

```liquid
{% if CurrentPerson.LastName == 'Decker' and CurrentPerson.FirstName == 'Ted' %}
    ...Prints when the logged in user's name is Ted Decker...
{% endif %}

{% if CurrentPerson.LastName == 'Decker' or CurrentPerson.LastName == 'Marbles' %}
    ...Prints when the logged in user's last name is Decker OR Marbles...
{% endif %}
```

## The Else Tag Can Be Used to Extend the If Tag

```liquid
{% if CurrentPerson.LastName == 'Decker' %}
    Hello Decker Family!
{% else %}
    Hello!
{% endif %}
```

## The Elseif Tag

```liquid
{% if CurrentPerson.LastName == 'Decker' %}
    Hi Decker Family!
{% elseif CurrentPerson.LastName == 'Marbles' %}
    Goodday Marbles Family!
{% else %}
    Hello!
{% endif %}
```

## If Blocks Will Stop Processing as Soon as a Condition is Met

```liquid
{% if CurrentPerson.LastName == 'Decker' %}
    Hi Decker Family!
{% elseif CurrentPerson.NickName == 'Ted' %}
    How are you, Ted?
{% else %}
    Hello!
{% endif %}
```

## A For Loop is Used to Repeat a Section of Logic for Many Items

```liquid
{% for currentGroupMembership in CurrentPerson.Members %}
    {{ currentGroupMembership.Group.Name }} <br/>
{% endfor %}
```

## Additional Parameters Will Go in the Opening Tag of the For Loop

```liquid
{% for currentGroupMembership in CurrentPerson.Members limit:3 %}
    {{ currentGroupMembership.Group.Name }} <br/>
{% endfor %}
```

## An Example

```liquid
{% assign onServingTeam = false %}
{% for membership in CurrentPerson.Members %}
    {% if membership.IsActive == true and membership.Group.IsActive == true %}
        {% if membership.Group.GroupType.GroupTypePurposeValue != null %}
            {% if membership.Group.GroupType.GroupTypePurposeValue.Value == 'Serving Area' %}
                {% assign onServingTeam = true %}
            {% endif %}
        {% endif %}
    {% endif %}
{% endfor %}
{% if onServingTeam == true %}
    Thank you for serving in our church!
{% else %}
    Looking for a place to volunteer? 
{% endif %}
```

## Filter Syntax

```liquid
{{ CurrentPerson | Attribute:'Allergy' }}
```

## Get a Subset of a List with the Where Filter

```liquid
{% assign activeMemberships = CurrentPerson.Members | Where:'GroupMemberStatus','Active' %}
{{ activeMemberships | Size }}
```

## Use the Select Filter to Transform the Entity Type

```liquid
{% assign activeMemberships = CurrentPerson.Members | Where:'GroupMemberStatus','Active' %}
{% assign activeGroups = activeMemberships | Select:'Group' | Where:'IsActive',true %}
{{ activeGroups | Size }}
```

## Transform the List of Groups to Their Group Types and Then Purposes

```liquid
{% assign activeMemberships = CurrentPerson.Members | Where:'GroupMemberStatus','Active' %}
{% assign activeGroups = activeMemberships | Select:'Group' | Where:'IsActive',true %}
{% assign groupPurposes = activeGroups | Select:'GroupType' | Select:'GroupTypePurposeValue' %}
{% assign servingTeams = groupPurposes | Where:'Value','Serving Area' %}
{{ servingTeams | Size }}
```

## Filters Can be Chained Together

```liquid
{% assign numberOfServingTeams = CurrentPerson.Members | Where:'GroupMemberStatus','Active' | Select:'Group' | Where:'IsActive', true | Select:'GroupType' | Select:'GroupTypePurposeValue' | Where:'Value','Serving Area' | Size %}
{% if numberOfServingTeams > 0 %}
    Thank you for serving in our church!
{% else %}
    Looking for a place to volunteer? 
{% endif %}
```

## The ToJSON Filter Turns Entities into a Readable Format of Key Value Pairs

```liquid
{{ CurrentPerson.Members | First | ToJSON }}
```

```json
{ "ArchivedByPersonAlias": null, "GroupMemberAssignments": [], "GroupMemberRequirements": [], "GroupRole": { "IsSystem": true, "GroupTypeId": 1, "Name": "Member", "Description": "Member of a group", "Order": 0, "MaxCount": null, "MinCount": null, "IsLeader": false...
```

## Use a JSON Formatter to Display the Data

```json
{ 
  "Id": 12345,
  "GroupId": 3,
  "PersonId": 456,
  "GroupRoleId": 1,
  "GroupRole": {
    "IsSystem": true,
    "GroupTypeId": 1,
    "Name": "Member",
    "IsLeader": false,
    "CreatedDateTime": null,
    "ModifiedDateTime": "2019-10-22T09:47:18.62",
    "CreatedByPersonAliasId": null,
    "ModifiedByPersonAliasId": 37830,
    "Id": 1
  }
}
```

## JSON for a Person Record

```json
{
    "Id": 12345,
    "FirstName": "Ted",
    "LastName": "Decker",
    "ConnectionStatusValue": {
        "DefinedTypeId": 4,
        "Value": "Attendee"
    },
    "PhoneNumbers": [
        {
            "Id": 212,
            "Number": 0123456789,
            "NumberTypeValue": {
                "Value": "Mobile"
            },
        },
        { ...Work Phone... }
    ]
}
```

## Access to the Object as a Property Allows Us to Easily Print the Value

```json
{
    "Id": 1234,
    "ConnectionStatusValueId": 64,
    "ConnectionStatusValue": {
        "DefinedTypeId": 4,
        "Value": "Attendee"
    }
}
```

```liquid
{{ CurrentPerson.ConnectionStatusValue.Value }}
```

## The Attribute Values Array is Terrible to Work With

```json
{
    "Id": 1234,
    "AttributeValues": [
        {
            "AttributeId": 740, 
            "EntityId": 30820, 
            "Value": "The Crossing"
        },
        { ...Another Attribute Value... }
    ]
}
```

## Entity Attributes Can be Accessed With the Attribute Filter

```liquid
{{ Person.AttributeValues | Where:'AttributeId',740 | First | Property:'Value' }}
```

```liquid
{{ CurrentPerson | Attribute:'Allergy' }}
```

## Some Other Filters

```liquid
{{ 'Now' | Date | DateAdd:7 | Date:'MM/dd/yy' }}
```

```liquid
{{ CurrentPerson | Group:'2','Active' }}
```

```liquid
{{ CurrentPerson | NearestCampus | Property:'Name' }}
```

## Retrieve Data with the Entity Command

```liquid
{% groupmember %}

{% endgroupmember %}
```

## Provide Information About the Desired Data with Parameters

```liquid
{% groupmember id:'1' %}
{% endgroupmember %}

{% groupmember ids:'1,2,3' %}
{% endgroupmember %}

{% groupmember where:'GroupId == 4' %}
{% endgroupmember %}
```

## Using the Entity Data

```liquid
{% groupmember id:'1' %}
    {{ groupmember.Person.FirstName }}
{% endgroupmember %}
```

```liquid
{% groupmember ids:'1,2,3' %}
    {{ groupmemberItems[0].Person.FirstName }}
{% endgroupmember %}

{% groupmember where:'GroupId == 4' %}
    {% assign firstMember = groupmemberItems | First %}
    {{ firstMember.Person.FirstName }}
{% endgroupmember %}
```

## The Personalize Command Allows for Dynamic Content

```liquid
{% personalize segment:'SegmentKey' requestfilter:'FilterKey' matchtype:'all' %}
  Content for people in this segment!
{% otherwise %}
  Content for everyone else!
{% endpersonalize %}
```
