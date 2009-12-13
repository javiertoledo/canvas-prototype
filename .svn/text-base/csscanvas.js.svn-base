// Conjunto de funciones basadas en script.aculo.us para permitir crear diagramas sobre un div con HTML+CSS+Javascript

//Código que se ejecuta al cargar la librería
document.write('<link href="csscanvas.css" rel="stylesheet" type="text/css" />');

//////////////////////////////////////////////////////////////////////////////////////
// Clase Segment
//  Representa un segmento de línea
//////////////////////////////////////////////////////////////////////////////////////
var Segment = Class.create();
Segment.prototype = {
	initialize: function (id){
		this.x=0;
		this.y=0;
		this.w=0;
		this.h=0;
		try {
		    this.element = document.createElement("<div id='"+id+"' class='line'></div>");
		} catch (e) {
		    this.element = document.createElement("div");
		    this.element.setAttribute("id", id);
			this.element.setAttribute("class","line");
		}
	},
	getElement: function(){
		return this.element;
	},
	update: function (x,y,w,h) {
		this.x=parseInt(x);
		this.y=parseInt(y);
		this.w=parseInt(w);
		this.h=parseInt(h);
		this.draw();
	},
	draw: function(){
		$(this.element).setStyle({top: this.y+'px', left: this.x+'px',width: this.w+'px', height: this.h+'px'});
	}
};

//////////////////////////////////////////////////////////////////////////////////////
// Clase Linea
//  Representa una linea quebrada
//////////////////////////////////////////////////////////////////////////////////////
var Line = Class.create();
Line.prototype = {
	initialize: function(id,variable,relacion,width){
		this.variable=variable;
		this.relacion=relacion;
		this.w=parseInt(width);
		try {
		    this.element = document.createElement("<div id='"+id+"'></div>");
		} catch (e) {
		    this.element = document.createElement("div");
		    this.element.setAttribute("id", id);
		}
		this.segmentos = new Array();
		this.segmentos[0]=new Segment(id+'0');
		this.segmentos[1]=new Segment(id+'1');
		this.segmentos[2]=new Segment(id+'2');
		this.element.appendChild(this.segmentos[0].getElement());
		this.element.appendChild(this.segmentos[1].getElement());
		this.element.appendChild(this.segmentos[2].getElement());
		this.variable.addLineUpdater(this);
		this.relacion.addLineUpdater(this);
	},
	getElement: function(){
		return this.element;
	},
	update: function(){
		wx=Math.round((parseInt(this.relacion.x())-parseInt(this.variable.x()))/2);
		wy=parseInt(this.relacion.y())-parseInt(this.variable.y());
		if (wx<0) {
			wx=-wx;
			x1=parseInt(this.relacion.x());
			x2=x1+wx;
			xm=x2;
			if (wy<0) {
				wy=-wy;
				y1=parseInt(this.relacion.y());
				y2=y1+wy;
			}
			else{
				y1=parseInt(this.variable.y());
				x2=x1;
				x1=xm;
				y2=y1+wy;
				wy=wy+this.w;
			}
		}
		else{
			x1=parseInt(this.variable.x());
			x2=x1+wx;
			xm=x2;
			if (wy<0) {
				y1=parseInt(this.relacion.y());
				x2=x1;
				x1=xm;
				y2=y1-wy;
				wy=this.w-wy;
			}
			else{
				y1=parseInt(this.variable.y());
				y2=y1+wy;
			}
		}
		this.segmentos[0].update(x1,y1,wx,this.w);
		this.segmentos[1].update(xm,y1,this.w,wy);
		this.segmentos[2].update(x2,y2,wx,this.w);
	},
	changeWidth: function(width){
		this.w=parseInt(width);
		this.update();
	}
};

//////////////////////////////////////////////////////////////////////////////////////
// Clase Variable
//  Representa una variable
//////////////////////////////////////////////////////////////////////////////////////
var Variable = Class.create();
Variable.prototype = {
	initialize: function(id){
		try {
		    this.element = document.createElement("<div id='"+id+"' class='bloquevariable'>"+id+"</div>");
		} catch (e) {
		    this.element = document.createElement("div");
		    this.element.setAttribute("id", id);
		    this.element.setAttribute("class", "bloquevariable");
		}
		this.element.innerHTML=id;
		this.lines = new Array();
		Draggables.addObserver(this);
		$(this.element).setStyle({top: '10px', left: '10px'});
	},
	getElement: function(){
		return this.element;
	},
	addLineUpdater: function(line){
		this.lines[this.lines.size()] = line; 
	},
	onDrag: function(eventName,draggable,event){
		if(draggable.element==this.element){
			this.lines.each(function(s){
				s.update();
			});
		}
	},
	x: function(){
		return this.element.getStyle("left");
	},
	y: function(){
		return this.element.getStyle("top");
	}
};

//////////////////////////////////////////////////////////////////////////////////////
// Clase Relation
//  Representa una relacion
//////////////////////////////////////////////////////////////////////////////////////
var Relation = Class.create();
Relation.prototype = {
	initialize: function(id){
		try {
		    this.element = document.createElement("<div id='"+id+"' class='bloquerelation'>"+id+"</div>");
		} catch (e) {
		    this.element = document.createElement("div");
		    this.element.setAttribute("id", id);
		    this.element.setAttribute("class", "bloquerelation");
		}
		this.element.innerHTML=id;
		this.lines = new Array();
		Draggables.addObserver(this);
		$(this.element).setStyle({top: '10px', left: '10px'});
	},
	getElement: function(){
		return this.element;
	},
	addLineUpdater: function(line){
		this.lines[this.lines.size()] = line; 
	},
	onDrag: function(eventName,draggable,event){
		if(draggable.element==this.element){
			this.lines.each(function(s){
				s.update();
			});
		}
	},
	x: function(){
		return this.element.getStyle("left");
	},
	y: function(){
		return this.element.getStyle("top");
	}
};

//////////////////////////////////////////////////////////////////////////////////////
// Clase Canvas
//  Representa el área de dibujo
//////////////////////////////////////////////////////////////////////////////////////
var Canvas = Class.create();
Canvas.prototype = {
	initialize: function(id){
		this.canvas=$(id);
		$(id).addClassName('canvas');
		Droppables.add(id,{
			canvas: this,
			accept: new Array("variable","relation"),
			onDrop: function(element){
				//Función que se llama cuando se suelta una variable sobre el canvas
				if (element.hasClassName('variable')) {
					this.canvas.addVariable();
				}
				else{
					this.canvas.addRelation();
				}
			}
		});
		this.variables = new Array();
		this.relations = new Array();
		this.lines = new Array();
	},
	addVariable : function(){
		var p=this.variables.size();
		var id='variable'+p;
		this.variables[p]=new Variable(id);
		this.canvas.appendChild(this.variables[p].getElement());
		this.draggable = new Draggable(id);
	},
	addRelation: function(){
		var p = this.relations.size();
		var id='relation'+p;
		this.relations[p]=new Relation(id);
		this.canvas.appendChild(this.relations[p].getElement());
		this.draggable = new Draggable(id);
		
		//PRUEBA DE AÑADIR LINEA///////////////////////////////////////////////
		this.lines[0]=new Line('milinea',this.variables[0],this.relations[p],3);
		this.canvas.appendChild(this.lines[0].getElement());
		this.lines[0].update();
		//PRUEBA DE AÑADIR LINEA///////////////////////////////////////////////
	},
	addLine: function(idvar,idrel){
		alert("yeah");
	}
};


//Función que convierte una lista en una lista de variables
function createVariables(list){
	Sortable.create(list,{constraint:false});
	var array = $(list).childElements();
	array.each(function(s){s.addClassName('variable');});
}

//Función que convierte una lista en una lista de relaciones
function createRelations(list){
	Sortable.create(list,{constraint:false});
	var array = $(list).childElements();
	array.each(function(s){s.addClassName('relation');});
}