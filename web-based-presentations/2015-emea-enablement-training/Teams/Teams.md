## Introduction to Teams
* ######Multiple developers can be part of a conjoined role within a domain called a team
* ######A team counts as one member of a domain and you control and manage a team
* ######Global teams that are controlled and managed by a system administrator

![Team](../images/team.png) <!-- .element: class="stretch" -->

--
## Teams and roles
#####The following team roles are available: view, edit, and admin.

* View
#####Domain level: view the details of an application but will not be able to clone the Git repository, SSH to the gear, stop or start the application, or embed add-on cartridges#

* Edit
#####Domain level: Adding cartridges, creating new applications, deleting applications, viewing and changing source code and triggering new deployments#

* Admin
#####Full control of the domain#
Notes:
######If you have a specific role, and you are on a team that has a different role, the effective role is the higher 
--
### Create Your "personal" Team
List your user's teams:

    [root@broker ~]# rhc team list
    You are a member of 0 teams.

Now, create a team called "personalteam". This will be the non-global team that
we will initially play with, and your user, teamuser1, will be the administrator
of it.

    [root@broker ~]# rhc team create personalteam
    Creating team 'personalteam' ... done
    You may now add team members using the 'rhc add-member' command

    [root@broker ~]# rhc team list
    Team personalteam (owned by teamuser1)
    --------------------------------------
      ID: 541737aa40164e4fad000004

    You are a member of 1 team.

Users can create their own teams, and then add teams to a domain. This allows
for reusing the team membership on multiple domains, rather than directly
maintaining the membership on each domain.
--
### Add the Other User to Your Team
The other user is "teamuser2". Go ahead and add them to your personal team:

    [root@broker ~]# rhc member add -t personalteam teamuser2
    Adding 1 viewer to team ... done

    [root@broker ~]# rhc team show personalteam
    Team personalteam (owned by teamuser1)
    --------------------------------------
      ID:      541737aa40164e4fad000004
      Members: teamuser2 (view)

Currently the user "teamuser2" only has view permissions on the team.

### Add the Team to the Domain
Now that we have a domain (teamapps) and a team (personalteam), we will combine
the two by adding the team to the domain:

    [root@broker ~]# rhc member list -n teamapps
    Login     Role          Type
    --------- ------------- ----
    teamuser1 admin (owner) user
    [root@broker ~]# rhc member-add -n teamapps --role edit personalteam --type team
    Adding 1 editor to domain ... done
    [root@broker ~]# rhc member list -n teamapps --all
    Name         Login     Role                    Type
    ------------ --------- ----------------------- ----
    teamuser1    teamuser1 admin (owner)           user
    personalteam           edit                    team
    teamuser2    teamuser2 edit (via personalteam) user

We see that the team "personalteam" has edit privileges on this domain,
teamapps. Additionally, passing --all shows us all users and we see that
"teamuser2" has edit permissions through his membership of personalteam.

Notes:
######Ideally customers would like to just use their current AD and sync from there.So to me this looks like they should be using global teams.But in the user guide Note 1. is reported that you are limited to 100 members.If they sync a group with more members than MAX_MEMBERS_PER_RESOURCE, oo-admin-ctl-team will display an error telling them the group has too many members.Membership limits are for performance, since team members are denormalized to all domains and applications the team has access to. They can raise the limit using the MAX_MEMBERS_PER_RESOURCE broker config variable
######https://access.redhat.com/documentation/en-US/OpenShift_Enterprise/2/html-single/User_Guide/index.html#Adding_Application_Developers_to_a_Team
