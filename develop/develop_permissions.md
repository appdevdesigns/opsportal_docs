[< Develop](Develop.md)
# Permissions

In our framework, we break down permissions into `Actions`, `Roles` and `Scopes`


1. [Actions](develop_permissions_actions.md)
2. [Roles](develop_permissions_roles.md)
3. [Scopes](develop_permissions_scopes.md)
4. [Limiting Routes](develop_permissions_limitRoutes.md)
5. [Scoping Routes](develop_permissions_scopingRoutes.md)

<!-- 

Scoping issues:
	1) limitRouteToScope() : for when your model directly relates to a USER
	2) limitRouteToUserActionScope() : for when your model actually contains the actionKey and User info
	3) example of using .scopeUsersForAction() : for when your model indirectly relates to a USER

-->


[< Develop](Develop.md)     
Next: [Actions >](develop_permissions_actions.md)