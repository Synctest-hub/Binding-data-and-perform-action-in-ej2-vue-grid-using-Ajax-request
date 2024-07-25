<template>
    <div id="app">
        <ejs-button cssClass='e-success' @click="click">Bind data via ajax</ejs-button>
        <div style="padding: 20px 17px 0 0">
            <ejs-grid ref="grid" :editSettings='editSettings' :toolbar='toolbar' allowPaging="true" height="320" :actionBegin="actionBegin" :actionComplete="actionComplete">
                <e-columns>
                    <e-column field='OrderID' headerText='Order ID' isPrimaryKey=true width='150'></e-column>
                    <e-column field='CustomerID' headerText='Customer Name' width='150'></e-column>
                    <e-column field='Freight' headerText='Freight' format="C" width='150'></e-column>
                    <e-column field='ShipCity' headerText='ShipCity' width='150' textAlign='Right'></e-column>
                </e-columns>
            </ejs-grid>
        </div>
    </div>
</template>

<script setup>
    import { provide, ref } from "vue";
    import { GridComponent as EjsGrid, ColumnDirective as EColumn, ColumnsDirective as EColumns, Page,Edit,Toolbar } from "@syncfusion/ej2-vue-grids";
    import { ButtonComponent as EjsButton } from "@syncfusion/ej2-vue-buttons";
    import { Ajax } from '@syncfusion/ej2-base';

    const grid = ref(null);
    let flag = false;
    const editSettings = { allowEditing: true, allowAdding: true, allowDeleting: true, mode: 'Normal' };
    const toolbar = ['Add', 'Edit', 'Delete', 'Update', 'Cancel'];
    const click = function () {
        const ajax = new Ajax("https://localhost:7284/Home/Getdata", 'POST'); // Use remote server host number instead ****
        ajax.send();
        ajax.onSuccess = (data) => {
            grid.value.ej2Instances.dataSource = JSON.parse(data);
        };
    };

    const actionBegin = function (e) {
        // Initially this.flag needs to be false in order to enter this condition
        if (!flag) {
            // Add and edit operations
            if (e.requestType === 'save' && (e.action === 'add')) {
                var editedData = e.data;
                // The default edit operation is cancelled
                e.cancel = true;
                // Here you can send the updated data to your server using Ajax call
                const ajax = new Ajax({
                    url: 'https://localhost:7284/Home/Insert',
                    type: 'POST',
                    contentType: 'application/json; charset=utf-8',
                    data: JSON.stringify({ value: editedData })
                }); // Use remote server host number instead ****
                ajax.onSuccess = () => {
                    // this.flag is enabled to skip this execution when grid ends add/edit
                    flag = true;
                    // The added/edited data will be saved in the Grid
                    grid.value.ej2Instances.endEdit();
                };
                ajax.onFailure = () => {
                    // Add/edit failed
                    // The this.flag is disabled if operation is failed so that it can enter the condition on next execution
                    flag = false;
                };
                ajax.send();
            }
            if (e.requestType === 'save' && (e.action === "edit")) {
                var editedData = e.data;
                // The default edit operation is cancelled
                e.cancel = true;
                // Here you can send the updated data to your server using Ajax call
                const ajax = new Ajax({
                    url: 'https://localhost:7284/Home/Update',
                    type: 'POST',
                    contentType: 'application/json; charset=utf-8',
                    data: JSON.stringify({ value: editedData })
                }); // Use remote server host number instead ****
                ajax.onSuccess = () => {
                    // this.flag is enabled to skip this execution when grid ends add/edit
                    flag = true;
                    // The added/edited data will be saved in the Grid
                    grid.value.ej2Instances.endEdit();
                };
                ajax.onFailure = () => {
                    // Add/edit failed
                    // The this.flag is disabled if operation is failed so that it can enter the condition on next execution
                    flag = false;
                };
                ajax.send();
            }

            if (e.requestType === 'delete') {
                var editedData = e.data;
                // The default delete operation is cancelled
                e.cancel = true;
                // Here you can send the deleted data to your server using Ajax call
                const ajax = new Ajax({
                    url: 'https://localhost:7284/Home/Delete',
                    type: 'POST',
                    contentType: 'application/json; charset=utf-8',
                    data: JSON.stringify({ key: editedData[0][grid.value.ej2Instances.getPrimaryKeyFieldNames()[0]] })
                }); // Use remote server host number instead ****
                ajax.onSuccess = () => {
                    // this.flag is enabled to skip this execution when grid deletes record
                    flag = true;
                    // The deleted data will be removed in the Grid
                    grid.value.ej2Instances.deleteRecord();
                };
                ajax.onFailure = () => {
                    // Delete failed
                    // The this.flag is disabled if operation is failed so that it can enter the condition on next execution
                    flag = false;
                };
                ajax.send();
            }
        }
    };

    const actionComplete = function (e) {
        if (e.requestType === 'save' || e.requestType === 'delete') {
            // The this.flag is disabled after operation is successfully performed so that it can enter the condition on next execution
            flag = false;
        }
    };

    provide('grid', [Page, Edit, Toolbar]);
</script>

<style>
    @import "../node_modules/@syncfusion/ej2-base/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-buttons/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-calendars/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-dropdowns/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-inputs/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-navigations/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-popups/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-splitbuttons/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-vue-grids/styles/tailwind.css";
    @import "../node_modules/@syncfusion/ej2-vue-grids/styles/material.css";
</style>
