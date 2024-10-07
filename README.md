# solr-functions
### Explanation
Class Initialization: The EmployeeDatabase class initializes with an empty dictionary to hold collections.

Create Collection: createCollection creates a new collection if it doesn't already exist.

Index Data: indexData indexes employee data into a specified collection, excluding a specified column.

Search by Column: searchByColumn retrieves records from a collection where a specific column matches a given value.

Get Employee Count: getEmpCount returns the count of employees in a specified collection.

Delete Employee by ID: delEmpById removes an employee from the collection based on their ID.

Get Department Facet: getDepFacet counts employees grouped by department.
### PROGRAM
```
import json
import requests
import pandas as pd

#Collection Creation
def create_collection(p_collection_name):
    url=f"http://localhost:8983/solr/admin/collections?action=CREATE&name={p_collection_name}&numShards=1&replicationFactor=1"
    print(requests.get(url).json())
    url = f"http://localhost:8983/solr/{p_collection_name}/schema"
    headers = {"Content-Type": "application/json"}
    data = {
    "add-field": {
      "name": "Department",
      "type": "string",
      "multiValued": False
    }
  }
    response = requests.post(url, headers=headers, data=json.dumps(data))
    print(f"Create Collection Response: {response.status_code}")

#Indexing Data
def indexData(p_collection_name,p_exclude_column):
    data=pd.read_csv("Employee.csv",encoding='latin-1')
    data = data.drop(columns=[p_exclude_column])
    data.columns = data.columns.str.replace(' ', '_')
    data=data.to_csv(index=False,encoding='utf-8')
    url=f"http://localhost:8983/solr/{p_collection_name}/update?commit=true"
    headers={"Content-Type":"application/csv"}
    response=requests.post(url,headers=headers,data=data)
    print(data)
    print(response)
    print(f"Response Status Code: {response.status_code}")
    print(f"Response Content: {response.text}")
    return response.json()


#Search By Column
def searchByColumn(p_collection_name,p_column_name,p_column_value):
    url=f"http://localhost:8983/solr/{p_collection_name}/select?indent=true&q={p_column_name}:{p_column_value}&wt=json"
    response = requests.get(url)
    results = response.json()['response']['docs']
    formatted_json = json.dumps(results, indent=4)
    print(formatted_json)

#Getting Employee Count
def getEmpCount(p_collection_name):
    url = f"http://localhost:8983/solr/{p_collection_name}/select?q=*:*&rows=0"
    response = requests.get(url)
    print(response.json()['response']['numFound'])

#Deleting Employee by ID
def delEmpById(p_collection_name, p_employee_id):
    url = f"http://localhost:8983/solr/{p_collection_name}/update?commit=true"
    data = json.dumps({'delete':{'query':f"Employee_ID:{p_employee_id}"}})
    headers = {"Content-Type": "application/json"}
    response = requests.post(url, headers=headers, data=data)
    print("Response Status Code:", response.status_code)
    print("Response Content:", response.text) 
    return response.json()

#Getting Count for the Groups under a Department
def getDepFacet(p_collection_name):
    url = f"http://localhost:8983/solr/{p_collection_name}/select?q=*:*&facet=true&facet.field=Department"
    response = requests.get(url)
    facets = response.json()['facet_counts']['facet_fields']['Department']
    print(f"Department Facets: {facets}")

v_nameCollection="joansjayauthers"
v_phoneCollection='joans_7216'
create_collection(v_nameCollection)
create_collection(v_phoneCollection)
getEmpCount(v_nameCollection)
indexData(v_nameCollection,'Department')
indexData(v_ phoneCollection, 'Gender')
delEmpById(v_nameCollection,'E02003')
getEmpCount(v_nameCollection)
searchByColumn(v_nameCollection,'Department','IT')
searchByColumn(v_nameCollection,'Gender' ,'Male')
searchByColumn(v_ phoneCollection,'Department','IT')
getDepFacet(v_nameCollection)
getDepFacet(v_phoneCollection)
```
### OUTPUT
![Screenshot 2024-10-08 010951](https://github.com/user-attachments/assets/14373d66-85ff-4b55-b959-d88acec5d373)
![Screenshot 2024-10-08 011207](https://github.com/user-attachments/assets/16aede40-ae32-4547-afe6-4333e0015f73)
![Screenshot 2024-10-08 011253](https://github.com/user-attachments/assets/80a0e4f6-968d-4be3-9ca5-2b4b44a6d2cc)
![Screenshot 2024-10-08 011626](https://github.com/user-attachments/assets/e0fa19d1-0880-4465-9441-34ba6f8f1a03)
![Screenshot 2024-10-08 011914](https://github.com/user-attachments/assets/e01f3bff-e0a6-4e69-8169-e74ec069ef21)
![Screenshot 2024-10-08 012057](https://github.com/user-attachments/assets/9d884ff3-82ba-4715-bbf9-654035aeb28f)
![Screenshot 2024-10-08 012106](https://github.com/user-attachments/assets/88522bc7-3fa1-4222-ac26-80c0894cc473)
![Screenshot 2024-10-08 012147](https://github.com/user-attachments/assets/7bbd8883-805e-43df-a711-af3a519dcbf9)
![Screenshot 2024-10-08 012224](https://github.com/user-attachments/assets/ef7836a5-0e2a-4ad8-9834-03854d155cec)

### OPERATION
Class Initialization: The EmployeeDatabase class initializes with an empty object to hold collections.

Create Collection: The createCollection method creates a new collection if it doesn't already exist.

Index Data: The indexData method indexes employee data into a specified collection, excluding the specified column. It uses destructuring to exclude the column.

Search by Column: The searchByColumn method retrieves records where a specified column matches a given value using the filter method.

Get Employee Count: The getEmpCount method returns the count of employees in a specified collection.

Delete Employee by ID: The delEmpById method removes an employee from the collection based on their ID, using filter to create a new array without the deleted employee.

Get Department Facet: The getDepFacet method counts employees grouped by department, using an object to tally counts.

