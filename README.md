# reactandfirebase
React and Firebase
 
import React, { Component } from 'react'
import * as firebase from 'firebase'

export default class FirebaseSample extends Component {

    constructor(){
        super();
        this.state={
            speed:10,
            allStory:[],
            todayStory:{}
        };
    }


    componentDidMount(){
        const rootRef = firebase.database().ref().child('react');
        const speedRef = rootRef.child('speed');
        speedRef.on('value',snap =>{
            this.setState({
                speed:snap.val()
            });
        });

        // add/write simple json file on firebase 
        // using set to change the ref of tree data with new one 
        //using update to add new fields in the ref of tree and it will not delete prev data
        const addref = firebase.database().ref().child('add');
        addref.set({
            username: "name",
            email: "email",
            profile_picture : "imageUrl"
        }, function(error) {
            if (error) {
                console.log(error);
              // The write failed...
            } else {
            console.log("Data Updated");
              // Data saved successfully!
            }
        });

           // add/write array json file on firebase 
        firebase.database().ref().child('allStories').push().update({
            name:"new Name",
            story:"new Story",
            image:"new Image"
        })

        // read simple json file on firebase
        const ref = firebase.database().ref().child('today');
        ref.on("value", snapshot=> {
            var newPost = snapshot.val();
            this.setState({
                todayStory:newPost
            });
            console.log("Name: " + newPost.name);
            console.log("Story: " + newPost.story);
            console.log("Image: " + newPost.imageUrl);
          });

          // read array json file on firebase
          const allref = firebase.database().ref().child('allStories');
          allref.on("value", snapshot=> {
              let alls = snapshot.val();
              let allStories =[];
              for (let i in alls){
                allStories.push({
                    id:i, 
                    name:alls[i].name,
                    story:alls[i].story,
                    image:alls[i].image
                });

              }

              console.log(allStories);

              this.setState({
                  allStory:allStories
              });

            });


    }

    render() {
        return (
            <div className="App">
               <h1> {this.state.speed}</h1>
               <h3>{this.state.todayStory.name}</h3>
               <h3>{this.state.todayStory.imageUrl}</h3>
               <h3>{this.state.todayStory.story}</h3>
               {this.state.allStory.map((value)=>{
                 return(
                     <div key={value.id}>
                         <h1>{value.name}</h1>
                         <h1>{value.image}</h1>
                         <h1>{value.story}</h1>
                     </div>
                 )
               })}
            </div>
        )
    }
}
