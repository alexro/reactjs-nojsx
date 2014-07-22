reactjs-nojsx
=============

Simple solution to avoid using JSX with ReactJS and preserve readability 

JSX is a special formatting language used by ReactJS framework to improve readability.
It can look like this:

 render: function(){
  
     return <div>
       <div class="clicker" onMouseDown={this.handleMouseDown}>
         Give me the message!
       </div>
       <div class="message">Message conveyed
         <span class="count">{this.state.count}</span> time(s)</div>
     </div>
     ;
   }
  
Mixing of HTML content with Javascript is great, but requires to have additional tools for processing that and converting into plain Javascript. The result will be:


 render: function(){
  
     return React.DOM.div(null, 
       React.DOM.div( {className:"clicker", onMouseDown:this.handleMouseDown}, 
 " Give me the message! "      ),
       React.DOM.div( {className:"message"}, "Message conveyed ",        React.DOM.span( {className:"count"}, this.state.count), " time(s)")
     )
     ;
   }


We could have written it in js from the beginning, but this version is much harder to read. Though, having it in plain js is good for working with TypeScript, and thus I created a simple routine to bridge the readability and nativeness.

Now we can buid the DOM like this:

 render: function () {
 
         var dom = [
             {
                 tag: React.DOM.div,
                 props: { className: "commentBoxLabel" },
                 content: "Hello, world! I am a CommentBoxLabel. " + this.state.data.value
             },
             {
                 tag: React.DOM.span,
                 props: { className: "commentBoxLabel" },
                 dom: [
                     {
                         tag: React.DOM.span,
                         props: { className: "commentBoxLabel" },
                         content: "Hello, world! I am a CommentBoxLabel. " + this.state.data.value
                     }
                 ]
             }
         ];
 
         return parse(dom);
     }
     
And the parse() function is that:

 function parse(dom, inner) {
 
     var items = [];
 
     for (var el in dom) {
 
         if (dom[el].dom) {
             items.push(dom[el].tag(dom[el].props, parse(dom[el].dom, inner || true)));
         } else {
             items.push(dom[el].tag(dom[el].props, dom[el].content));
         }
     }
 
     if (inner) {
         return items.length == 1 ? items[0] : items;
     } else {
         return items.length == 1 ? items[0] : React.DOM.section(null, items);
     }
 }



