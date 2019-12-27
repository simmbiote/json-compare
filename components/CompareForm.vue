<template>
    <div>
        <form  @submit.prevent="compareObjects" style="margin-bottom: 40px;" >
            <textarea v-model="oldJsonRaw"  placeholder="Paste LHS Json..." name="json-1" id="json-1" cols="30" rows="5"></textarea>
            <textarea v-model="newJsonRaw"  placeholder="Paste RHS Json..."   name="json-2" id="json-2" cols="30" rows="5"></textarea>
            <div>
                <button type="submit" class="btn-primary btn"  >Compare</button>
            </div>
            </form>
            <div class="error text-red" v-for="(item, $index) in errors" :key="$index">{{item}}</div>

            <div id="results-area" :class=activeView v-if="items.length > 0">
            <p class="text-center">
                <a class="btn btn-small btn-info"   @click="setActiveView('all')" >{{items.length}} total items </a>
                <a class="btn btn-small btn-success" :v-if="passCount > 0"  @click="setActiveView('pass')" >{{passCount}} passed </a>
                <a class="btn btn-small btn-danger" :v-if="failCount > 0"  @click="setActiveView('fail')" >{{failCount}} fails </a>
                <a class="btn btn-small btn-warning" :v-if="investigateCount > 0"  @click="setActiveView('investigate')" >{{investigateCount}} to manually check </a>
            </p>

            <table class="table table-striped table-bordered" >
                    <thead>
                        <tr >
                            <th id="th_field">Field</th>  
                            <th id="th_exist">type</th>  
                            <th id="th_exist">Both exist</th>  
                            <th id="th_value">Value match</th>  
                            <th id="th_type">Type match</th>  
                            <th id="th_value">Length match</th>  
                            <th id="th_value">Result</th>  
                            <th id="th_value">Notes</th>  
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(item, $index) in items" :key="$index" :class=item.class>
                            <td class="text-left">{{item.field}}</td>  
                            <td>{{item.type}}</td>  
                            <td>{{item.both_exist}}</td>  
                            <td class="">{{item.value_match}}</td>  
                            <td>{{item.type_match}}</td>  
                            <td>{{item.length_match}}</td>  
                            <td>{{item.class}}</td>  
                            <td> 
                                <div v-if="item.class !== 'pass'">
                                    <div><strong>LHS:</strong> {{item.value_old}}</div>
                                    <div><strong>RHS:</strong> {{item.value_new}}  </div>
                                </div>
                            </td>  
                        </tr>
                    </tbody>
                </table>
            </div>
    </div>
</template>

<style >
#results-area tbody tr {display: none;}
#results-area.all tbody tr {display: table-row;}
#results-area.pass tbody tr.pass {display: table-row;}
#results-area.fail tbody tr.fail {display: table-row;}
#results-area.investigate tbody tr.investigate {display: table-row;}
</style>
<script>

 const  tableItemsReference = [
            // { field: 'products.name', both_exist: false, type_match: false, value_match: false }, 
        ];
    export default {
      data() {
        return {
          errors: [],
          failCount: 0,
          investigateCount: 0,
          passCount: 0,
          activeView: 'all',
          count: 0,
          oldJson: {},
          newJson: {},
          oldJsonRaw: "",
          newJsonRaw: "",
          items: tableItemsReference
        }
      },
      created() { 
      },
      mounted() { 
      }, 
      methods: {
         
        columnCompare: class Compare {
          constructor(oldObject, newObject, field) {
            this.oldObject = oldObject;
            this.newObject = newObject;
            this.field = field;
            return {
              type: this.type(),
              both_exist: this.both_exists(),
              type_match: this.type_match(),
              length_match: this.length_match(),
              value_match: this.value_match(),
              class: this.result_class(),
              value_old: this.oldObject[this.field],
              value_new: !this.newObject || this.newObject === undefined ? 'undefined' : this.newObject[this.field],
            }
          } 

          type() {
            return typeof this.oldObject[this.field];
          }

          both_exists() {
            if (!this.newObject || !this.oldObject) return false;
            return this.oldObject[this.field] !== undefined && this.newObject[this.field] !== undefined
          }

          type_match() {
            if (!this.both_exists()) return false;
            return typeof this.oldObject[this.field] === typeof this.newObject[this.field]
          }

          length_match() {
            if (!this.both_exists()) return false;
            if (this.newObject[this.field] === null || this.oldObject[this.field] === null) return false;
            return this.oldObject[this.field].length == this.newObject[this.field].length;
          }

          value_match() {
            if (!this.both_exists()) return false;
            return this.type() === "object" ? "-" : this.both_exists() && this.oldObject[this.field] == this.newObject[this.field]
          }

          result_class(){
            if(!this.both_exists() || !this.value_match())  return "fail";
            if(!this.type_match() || !this.length_match()) return "investigate";
                return "pass"
          }

        },
         setActiveView(view){
                this.activeView = view;
          },
          compareLoop: function (LHSObject, RHSObject, previousKey = '') {
            let objectType = typeof LHSObject;
            let fieldName = previousKey ? `${previousKey}.` : ``;

            if(objectType === 'object' && LHSObject !== null && LHSObject !== undefined){
               try {
                    Object.keys(LHSObject).forEach(key => {
                    let compare = new this.columnCompare(LHSObject, RHSObject, key);
                    this.items.push({
                    field: `${fieldName}${key}`,
                    ...compare
                    });

                    switch(compare.class){
                        case "fail":
                         this.failCount++;
                        break;
                        case "investigate":
                            this.investigateCount++;
                        break;
                        case "pass":
                            this.passCount++;
                        break;
                    }
                    if (typeof LHSObject[key] === 'object')   this.compareLoop(LHSObject[key], RHSObject[key], key);
                });
               } catch(e){
                   console.log("ERROR... ", objectType, typeof LHSObject, LHSObject)
                   console.error(e.message)
               }
            }
        },
        compareObjects: async function ()  {
        this.errors = [];
          try {
            JSON.parse(this.oldJsonRaw);
          } catch (e) {
            console.error('Can\'t parse');
            this.errors.push("LHS JSON data provided is invalid.")
          }

          try {
            JSON.parse(this.newJsonRaw);
          } catch (e) {
            console.error('Can\'t parse');
            this.errors.push("RHS JSON data provided is invalid.")
          }

          if (this.errors.length > 0) return false;
          this.errors = [];
          this.items = [];

          const parsedOldJson = JSON.parse(this.oldJsonRaw);
          const parsedNewJson = JSON.parse(this.newJsonRaw);

          this.compareLoop(parsedOldJson, parsedNewJson)
 
        }
      }
    }
</script>
 