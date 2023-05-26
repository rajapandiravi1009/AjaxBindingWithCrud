# Binding Grid data with CRUD operation using AJAX

We can use Grid **dataSource** property to bind the datasource to Grid from external **AJAX** request. In the below code we have fetched the datasource from the server with the help of AJAX request and provided that to **dataSource** property by using onSuccess event of the ajax.

```
<div>
@Html.EJS().Button("btn").Content("Bind data via AJAX").CssClass("e-flat").Render()

@Html.EJS().Grid("Grid").EditSettings(e => { e.AllowAdding(true).AllowEditing(true).AllowDeleting(true); }).Columns(col =>
{
    col.Field("OrderID").HeaderText("Order ID").IsPrimaryKey(true).Width("130").Add();
    col.Field("EmployeeID").HeaderText("Employee ID").Width("150").Add();
    col.Field("CustomerID").HeaderText("CustomerID").Width("70").Add();
    col.Field("ShipCity").HeaderText("Ship City").Width("70").Add();

}).AllowPaging(true).AllowSorting(true).ActionComplete("actionComplete").ActionBegin("actionBegin").Toolbar(new List<string>() { "Add", "Edit", "Delete", "Update", "Cancel" }).Render()

</div>

<script>
    let button = document.getElementById('btn');
    button.addEventListener("click", function (e) {
        let ajax = new ej.base.Ajax("/Home/Getdata", "POST");
        ajax.send();
        ajax.onSuccess = function (data) {
            var grid = document.getElementById('Grid').ej2_instances[0];
            grid.dataSource = JSON.parse(data);
        };
    });
</script>
```

To perform the **CRUD** actions using **AJAX** here in the actionBegin event, we have cancel the default CRUD operations using arguments **cancel** property. Now you can dynamically call your server method using ajax along with the corresponding data received in the actionBegin event to update the data in your server. In ajax success event you can use the Grid’s endEdit and deleteRecord methods to add/edit and delete the corresponding data respectively in the Grid.

This can be handled using a flag variable which is disabled in the actionComplete and ajax failure events so that it can enter the condition on next execution. This is demonstrated in the below code snippet,

```
<script>
    var flag = false;
    function actionBegin(e) {
        // Initially flag needs to be false in order to enter this condition
        if (!flag) {
        var grid = document.getElementById('Grid').ej2_instances[0];
            // Add and edit operations
            if (e.requestType == 'save' && (e.action == 'add')) {
                var editedData = e.data;
                // The default edit operation is cancelled
                e.cancel = true;
                // Here you can send the updated data to your server using ajax call
                var ajax = new ej.base.Ajax({
                    url: '/Home/Insert',
                    type: 'POST',
                    contentType: 'application/json; charset=utf-8',
                    data: JSON.stringify({ value: editedData })
                });
                ajax.onSuccess = (args) => {
                    // Flag is enabled to skip this execution when grid ends add/edit
                    flag = true;
                    // The added/edited data will be saved in the Grid
                    grid.endEdit();
                }
                ajax.onFailure = (args) => {
                    // Add/edit failed
                    // The flag is disabled if operation is failed so that it can enter the condition on next execution
                    flag = false;
                }
                ajax.send();
            }
            if (e.requestType == 'save' && (e.action == 'edit')) {
                var editedData = e.data;
                // The default edit operation is cancelled
                e.cancel = true;
                // Here you can send the updated data to your server using ajax call
                var ajax = new ej.base.Ajax({
                    url: '/Home/Update',
                    type: 'POST',
                    contentType: 'application/json; charset=utf-8',
                    data: JSON.stringify({ value: editedData })
                });
                ajax.onSuccess = (args) => {
                    // Flag is enabled to skip this execution when grid ends add/edit
                    flag = true;
                    // The added/edited data will be saved in the Grid
                    grid.endEdit();
                }
                ajax.onFailure = (args) => {
                    // Add/edit failed
                    // The flag is disabled if operation is failed so that it can enter the condition on next execution
                    flag = false;
                }
                ajax.send();
            }
            if (e.requestType == 'delete') {
                var editedData = e.data;
                // The default delete operation is cancelled
                e.cancel = true;
                // Here you can send the deleted data to your server using ajax call
                var ajax = new ej.base.Ajax({
                    url: '/Home/Delete',
                    type: 'POST',
                    contentType: 'application/json; charset=utf-8',
                    data: JSON.stringify({ key: editedData[0][grid.getPrimaryKeyFieldNames()[0]] })
                })
                ajax.onSuccess = (args) => {
                    // Flag is enabled to skip this execution when grid deletes record
                    flag = true;
                    // The deleted data will be removed in the Grid
                    grid.deleteRecord();
                }
                ajax.onFailure = (args) => {
                    // Delete failed
                    // The flag is disabled if operation is failed so that it can enter the condition on next execution
                    flag = false;
                }
                ajax.send();
            }
        }
    }

    function actionComplete(e) {
        if (e.requestType === 'save' || e.requestType === 'delete') {
            // The flag is disabled after operation is successfully performed so that it can enter the condition on next execution
            flag = false;
        }
    }
</script>
```

![](https://github.com/rajapandiravi1009/AjaxBindingWithCrud/blob/main/ezgif-5-a45c6cad66.gif)

### API Links:

  * https://ej2.syncfusion.com/documentation/api/grid/#actioncomplete
  * https://ej2.syncfusion.com/documentation/api/grid/#endedit
  * https://ej2.syncfusion.com/documentation/api/grid/#deleterecord
  * https://ej2.syncfusion.com/documentation/api/grid/#actionbegin
