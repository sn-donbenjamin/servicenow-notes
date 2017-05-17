# ServiceNow Notes
Feel free to ask Issues or push PRs

## API
- [Client](https://developer.servicenow.com/app.do#!/api_doc?v=istanbul&id=client)
- [Server](https://developer.servicenow.com/app.do#!/api_doc?v=istanbul&id=server)

### Common APIs

#### [GlideForm](https://developer.servicenow.com/app.do#!/api_doc?v=istanbul&id=c_GlideFormAPI)
- `g_form.save()` save and reload form

#### [GlideAjax](https://developer.servicenow.com/app.do#!/api_doc?v=istanbul&id=c_GlideAjaxAPI)

#### [GlideRecord](https://developer.servicenow.com/app.do#!/api_doc?v=istanbul&id=c_GlideRecordScopedAPI)

## Common global Objects
- `gs`, `system` [GlideSystem](https://developer.servicenow.com/app.do#!/api_doc?v=istanbul&id=c_GlideSystemScopedAPI)

- `current` Current [GlideRecord](https://developer.servicenow.com/app.do#!/api_doc?v=istanbul&id=c_GlideRecordScopedAPI), (read, write). Only avaiable when writing business rules.
  
- `previous` Previous
  [GlideRecord](https://developer.servicenow.com/app.do#!/api_doc?v=istanbul&id=c_GlideRecordScopedAPI) (read). Only avaiable when writing business rules.

## Roles
- ITIL users for impersonation: User, Joe Employe, Beth Anglin (has some other roles such as catalog_manager)

## UI Action
### Conditions

Are always evaluated server-side. Use `gs` and `current` to access user and
current record data.

### Scripts

Use
[GlideAjax](https://developer.servicenow.com/app.do#!/api_doc?v=istanbul&id=c_GlideAjaxAPI) to perform asynchronous requests.
- `GlideAjax.addParam("sysparm_yadda-yadda")` sysparm, not sysparam!!.

**Example**
```javascript
// UI Action Condition

// Checks if the current user has the role "admin"
// `current` is a GlideRecord. Remember the fields (and roles and tables) names are 
// usually preceded by "u_"
gs.hasRole("admin") && current.u_active == false



// UI Action Script

function setActiveToTrue() {
  // Specify which Script Include should be used
  var ga = new GlideAjax('ToggleActive');

  // Get current item unique Id
  var sys_id = g_form.getUniqueValue();

  // Required. Specify which method from the previous script will be used 
  ga.addParam('sysparm_name','activate');

  // Additional addParams will pass parameters to the previously specified method
  // Can be accessed through "this.getParameter("sysparm_sys_id")"
  ga.addParam('sysparm_sys_id', sys_id);


  // Perform the request with specified callback
  // The response is a simple xml document
  // `answer` attribute is the method return value
  ga.getXML(function(response) {
    var answer = response.responseXML.documentElement.getAttribute("answer");
    alert(anwer)
  });
}



// Script Include

// Remember to check "[ x ] Client Callable"
// server side executed

var ToggleActive = Class.create();
ToggleMovie.prototype = Object.extendsObject(AbstractAjaxProcessor, {

  activate: function() {
    var sys_id = this.getParameter("sysparm_sys_id");
    var table = "u_table_name";
    var field = "u_active";
    var gr = new GlideRecord(table);
    // Get desired item
    gr.get(sys_id);
    gr.setValue(field, true);
    gr.update();

    // Cant find any reference about action object. If u know please pr!!
    action.setRedirectURL(current);

    return "The field value is: " + gr[field];
  },

  type: "ToggleMovie"
});
```


## Script Includes
- `action.setRedirectURL()`