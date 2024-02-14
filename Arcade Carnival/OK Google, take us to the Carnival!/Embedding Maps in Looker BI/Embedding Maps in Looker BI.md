# ```Embedding Maps in Looker BI```


## ```Look #1- Choropleth Map: Plot carriers operating count by state on Maps```
1.In the Looker Navigation menu, click **Explore**.


2.Under **FAA**, click **Flights**.


![image](https://github.com/ROUSHNI1/Google-Cloud-Arcade-Challenge/assets/79622917/f46a4802-e54b-4075-933c-4ee0a6fe4406) 

3.The available **dimensions** and **measures** will be listed in the data panel for Flights.

4.Under **Carriers** > **Measures**, click **Count**

![image](https://github.com/ROUSHNI1/Google-Cloud-Arcade-Challenge/assets/79622917/3e5bcb46-4ae2-46df-8979-5d8ddf6bdac3)

5.Under **Aircraft Origin** > **Dimensions**, select **State**.

![image](https://github.com/ROUSHNI1/Google-Cloud-Arcade-Challenge/assets/79622917/9ad7b1f9-7743-438b-847b-f6bb4c300092)

6.Click **Run**.

7.Click the arrow next to **Visualization** to expand the window.

8.Once the Visualization window has expanded, Choose the **Map Visualization**.
9.A map should appear with US states.
10.Click on **edit** option. Go to the **value** tab. Toggle the **reverse color scale** option.

![image](https://github.com/ROUSHNI1/Google-Cloud-Arcade-Challenge/assets/79622917/0b017838-ce56-4676-9b55-e8bff483431f)

11. Save this **visualization** as a dashboard. Click on settings icon and then click on **save as a new dashboard**.
12. Title the Dashboard as **Carriers count against states**.

 
# 2 - Map with Lines: Count of Flights connecting a state
### Now go to Look in to main menu LookML 
⚪️ Turn on Development mode Toggle 
then Go to **File Browser** > ```qwiklabs-flights-maps.model.lkml```

![Screenshot (655)](https://github.com/ROUSHNI1/Google-Cloud-Arcade-Challenge/assets/79622917/3bd7cf3f-5520-4478-86f1-e484fe37d059)





## ```qwiklabs-flights-maps.model``` File

Replace this content with your file 

```bash
connection: "bigquery_public_data_looker"
# include all views in this project
include: "*.view"
include: "/z_tests/*.lkml"

map_layer: data_area {
  file: "maps/US_West_Midwest.topojson"
}

explore: airports {
  group_label: "FAA"
}

explore: flights {
  group_label: "FAA"
  description: "Start here for information about flights!"
  join: carriers {
    type: left_outer
    sql_on: ${flights.carrier} = ${carriers.code} ;;
    relationship: many_to_one
  }

  join: aircraft {
    type: left_outer
    sql_on: ${flights.tail_num} = ${aircraft.tail_num} ;;
    relationship: many_to_one
  }

  join: aircraft_origin {
    from: airports
    type: left_outer
    sql_on: ${flights.origin} = ${aircraft_origin.code} ;;
    relationship: many_to_one
    fields: [full_name, city, state, code, map_location]
  }

  join: aircraft_destination {
    from: airports
    type: left_outer
    sql_on: ${flights.destination} = ${aircraft_destination.code} ;;
    relationship: many_to_one
    fields: [full_name, city, state, code, map_location]
  }

  join: aircraft_models {
    sql_on: ${aircraft.aircraft_model_code} = ${aircraft_models.aircraft_model_code} ;;
    relationship: many_to_one
  }
}


# Place in `qwiklabs-flights-maps` model

explore: +flights {
  
    query: roushni_quicklab_task_1{
      dimensions: [aircraft_origin.state]
      measures: [carriers.count]
    }
  }


# Place in `qwiklabs-flights-maps` model
explore: +flights {
    query: roushni_quicklab_task_2{
      dimensions: [aircraft_destination.map_location, aircraft_origin.map_location]
      measures: [count]
      filters: [
        aircraft_destination.state: "CA,WA,CO,NV,UT,AK,HI,OR,LA,ID,WY",
        aircraft_origin.city: "ATLANTA^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ",
        flights.arrival_year: "2004"
      ]
  }
}

# Place in `qwiklabs-flights-maps` model

explore: +flights {
    query: roushni_quicklab_task_3 {
      dimensions: [aircraft.region]
      measures: [aircraft.count]
    }
  }


```


## ```aircraft.view``` File

Replace this content with your file 

```bash

view: aircraft {
  sql_table_name: `cloud-training-demos.looker_flights.aircraft` ;;

  dimension: tail_num {
    type: string
    primary_key: yes
    sql: rtrim(${TABLE}.tail_num) ;;
  }

  dimension: address1 {
    type: string
    sql: ${TABLE}.address1 ;;
  }

  dimension: address2 {
    type: string
    sql: ${TABLE}.address2 ;;
  }

  dimension_group: air_worth {
    type: time
    timeframes: [time, date, week, month, year, raw]
    convert_tz: no
    datatype: date
    sql: ${TABLE}.air_worth_date ;;
  }

  dimension: aircraft_engine_code {
    type: string
    sql: ${TABLE}.aircraft_engine_code ;;
  }

  dimension: aircraft_engine_type_id {
    type: number
    sql: ${TABLE}.aircraft_engine_type_id ;;
  }

  dimension: aircraft_model_code {
    type: string
    sql: ${TABLE}.aircraft_model_code ;;
  }

  dimension: aircraft_serial {
    type: string
    sql: ${TABLE}.aircraft_serial ;;
  }

  dimension: aircraft_type_id {
    type: number
    sql: ${TABLE}.aircraft_type_id ;;
  }

  dimension_group: cert_issue {
    type: time
    timeframes: [time, date, week, month, year, raw]
    convert_tz: no
    datatype: date
    sql: ${TABLE}.cert_issue_date ;;
  }

  dimension: certification {
    type: string
    sql: ${TABLE}.certification ;;
  }

  dimension: city {
    type: string
    sql: ${TABLE}.city ;;
  }

  dimension: country {
    type: string
    map_layer_name: countries
    sql: ${TABLE}.country ;;
  }

  dimension: county {
    type: string
    sql: ${TABLE}.county ;;
  }

  dimension: fract_owner {
    type: string
    sql: ${TABLE}.fract_owner ;;
  }

  # Don't use this one. It complicates the custom measure exercise.
  # Can't just hide it because hidden fields still show up as suggestions in custom fields.
  # dimension_group: last_action {
  #   hidden: yes
  #   type: time
  #   timeframes: [time, date, week, month, raw]
  #   convert_tz: no
  #   datatype: date
  #   sql: ${TABLE}.last_action_date ;;
  # }

  dimension: last_action_year {
    type: number
    sql: EXTRACT(YEAR FROM ${TABLE}.last_action_date) ;;
  }

  dimension: mode_s_code {
    type: string
    sql: ${TABLE}.mode_s_code ;;
  }

  dimension: name {
    type: string
    sql: ${TABLE}.name ;;
  }

  dimension: region {
    type: string
    case: {
      when: {
        sql: ${state} in ('WA','OR','CA','NV','UT','WY','ID','MT','CO','AK','HI') ;;
        label: "West"
      }
      when: {
        sql: ${state} in ('AZ','NM','TX','OK') ;;
        label: "Southwest"
      }
      when: {
        sql: ${state} in ('ND','SD','MN','IA','WI','MN','OH','IN','MO','NE','KS','MI','IL') ;;
        label: "Midwest"
      }
      when: {
        sql: ${state} in ('MD','DE','NJ','CT','RI','MA','NH','PA','NY','VT','ME','DC') ;;
        label: "Northeast"
      }
      when: {
        sql: ${state} in ('AR','LA','MS','AL','GA','FL','SC','NC','VA','TN','KY','WV') ;;
        label: "Southeast"
      }
      else: "Unknown"
    }
    map_layer_name: data_area
    drill_fields: [state]
  }


  dimension: registrant_type_id {
    type: number
    sql: ${TABLE}.registrant_type_id ;;
  }

  dimension: state {
    type: string
    sql: ${TABLE}.state ;;
  }

  dimension: status_code {
    type: string
    sql: ${TABLE}.status_code ;;
  }

  dimension: year_built {
    # type: date_year
    # sql: DATE(nullif(${TABLE}.year_built,0), 01, 01) ;;   # makes the SQL too clunky

    type: number
    sql: nullif(${TABLE}.year_built,0) ;;
    value_format_name: id
  }

  dimension: zip {
    type: zipcode
    sql: ${TABLE}.zip ;;
  }

  measure: count {
    type: count
    drill_fields: [name]
  }
}

```


### ```Now watch the video carefully...``` --https://www.youtube.com/watch?v=meOqNIMU93Y
