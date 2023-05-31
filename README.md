# OffBalanceDocs

Home of various documentations.

## API Contract (Client)

### `/client`

* Purpose: Used to check if the server is alive.
* Method: `GET`
* Request: `N/A`
* Response:
  * `200 OK`

    ```javascript
    {
      "status": String, // e.g. "success"
      "message": String // e.g. "{Method} @ {Timestamp}"
    }
    ```

### `/client/datadump`

* Purpose: Used to retrieve all new data since last request.
* Method: `GET`
* Request: `N/A`
* Response:
  * `200 OK`

    ```javascript
    {
      "status": String, // e.g. "success"
      "data": {
        "locations": [{ // locations that the rover has been
          "timestamp": Date, // time of location data
          "x": Number, // x index of rover
          "y": Number, // y index of rover
          "direction": String // current bearing of rover
        }],
        "vertices": [{ // newly identified vertices
          "id": Number, // vertice id
          "x": Number, // x index of vertice
          "y": Number, // y index of vertice
          "options": {
            String: Bool, 
            String: Bool,
            ...
          } // heading options for vertices, true-false specifies whether it has been traversed
        }],
        "edges": [{ // newly idenfied edges
          "vertices": [Number, Number], // edge vertex ids
          "weight": Number // edge length
        }],
        "shortest": [Number] // list of vertex ids containing the shortest path between any two nodes
                             // first id is the start and last id is the final node
      }
    }
    ```

## API Contract (Data)

Note that below, all of the values are just examples of how the data should look and these are not hard coded

### `/data/start`

* Purpose: Used to send the first node (also the node the rover starts at).
* Method: `POST`
* Request: 
  * 
    ```javascript
    {
        "x" : Number, // the x coordinate of the rover when starting, is hard coded as 0.
        "y" : Number, // the y coordinate of the rover when starting, is hard coded as 0.
        "options" : { // bearings of all possible turns
            String : Bool, 
            String : Bool
        }
    }
    ```
* Response:
  * `200 OK` 
    `success`


### `/data/update`

* Purpose: Used to update the live location of the node.
* Method: `POST`
* Request: 
  * 
    ```javascript
    {
      {
          "timestamp" : Date, // the time in ISO format
          "x" : Number, // current x coordinate
          "y" : Number, // current y coordinate
          "direction" : String // the direction you are headed in (this is a string but is in degrees)
      }
    }
    ```
* Response:
  * `200 OK`
    `success`

### `/data/node`

* Purpose: Used to add a vertice to the graph of nodes (automatically checks if node exists and also adds edge automatically).
* Method: `POST`
* Request: 
  * 
    ```javascript
    {
      {
          "x" : Number, // x coordinate of node
          "y" : Number, // y coordinate of node
          "options" : { // the bearings of turns at the node can be more or less than 2, just an example
              String : Bool,
              String : Bool
          },
          "weight": Number,
          "parentDirection" : String, // bearing of the parent node in relation to this edge (useful to set this option as true in the previous node)
          "childDirection" : String // bearing of the child node in relation to this edge (useful to set this option as true in the current node)
      }
    }
    ```
* Response:
  * `200 OK` 
    ```javascript
      { 
          String : Bool,
          String : Bool
      } // sends json with unexplored/explored options
    ```

### `/data/clear`

* Purpose: Clears and resets all data.
* Method: `GET`
* Request: `N/A`
* Response:
  * `200 OK`
    `success`
