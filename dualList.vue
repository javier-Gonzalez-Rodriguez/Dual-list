<template>
    <!-- lista de lotes -->
    <div id="dual-list-app" class="dual-list-container">
        <!-- Lista de elementos disponibles -->
        <div class="list-container">
            <h3 class="card-title">{{ tituloDisponibles }}</h3>
            <div class="selectable-list">
                <div 
                    :class="{'selectable-item': true, 'selectable-item-selected': element.selected == true}" 
                    v-for="(element, index) in elementsAvalibles" 
                    :key="'selectable-items-' + index"
                    @click="selectItemAvalibles(element.id)"
                    >
                    
                    <slot :element="element">
                        {{ element }}
                    </slot>
                </div>
            </div>
        </div>
        <!-- Botones para mover los elementos de una tabla a otra -->
        <div class="list-container-buttons">
            <div>
                <button @click="moveToTarget('addSimp')">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 20 20" fill="none">
                        <path d="M6 4L14 10L6 16" stroke="black" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                </button>
                <button @click="moveToTarget('addAll')">
                    <svg xmlns="http://www.w3.org/2000/svg" width="30" height="20" viewBox="0 0 30 20" fill="none">
                        <path d="M6 4L14 10L6 16" stroke="black" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                        <path d="M16 4L24 10L16 16" stroke="black" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                </button>
                <button @click="moveToTarget('rmSimp')">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 20 20" fill="none">
                        <path d="M14 4L6 10L14 16" stroke="black" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                </button>
                <button @click="moveToTarget('rmAll')">
                    <svg xmlns="http://www.w3.org/2000/svg" width="30" height="20" viewBox="0 0 30 20" fill="none">
                        <path d="M24 4L16 10L24 16" stroke="black" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                        <path d="M14 4L6 10L14 16" stroke="black" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                </button>
            </div>
        </div>

        <!-- Lista de elementos seleccionados -->
        <div class="list-container">
            <h3 class="card-title">{{ tituloSeleccionados }}</h3>
            <div class="selectable-list">
                <div v-for="(element, index) in selectedItems" :key="'items-selected-' + index" :class="{'selectable-item': true, 'selectable-item-selected': element.selected == true}" @click="selectItemAdded(element.id)">
                    <slot :element="element">
                        {{ element }}
                    </slot>
                </div>
            </div>
        </div>
        <!-- Botones para ordenar los elementos de la lista de elementos seleccionados -->
        <div class="list-container-buttons">
            <div>
                <button @click="moveToTarget('up')">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 20 20" fill="none">
                        <path d="M4 14L10 6L16 14" stroke="black" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                </button>
                <button @click="moveToTarget('down')">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 20 20" fill="none">
                        <path d="M4 6L10 14L16 6" stroke="black" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                </button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'DualListApp',
        props: {
            //elementos a elegibles para agregar(lista izquierda)
            elements: {
                type: Array,
                required: true
            },
            tituloDisponibles: { type: String, default: "Elementos disponibles" },
            tituloSeleccionados: { type: String, default: "Elementos Agregados" },
        },
        data(){
            return {
                //elementos agregados(lista derecha)
                selectedItems: [],
                elementsAvalibles: [],
            }
        },
        mounted() {          
            //elementos a insetar en la lista de elementos disponibles
            this.elementsAvalibles.push(...this.elements);
        },
        methods: {
            /**
             * desplaza los elmentos de la lista hacia una lista u otra asi como hacia arriba o hacia abajo 
             * @param accion accion que se va a realizar tras pulsar el boton
             */
            moveToTarget(accion){
                switch(accion){
				    case 'addSimp':
                        var idxList = [];

                        this.elementsAvalibles.forEach((object, idx) => {
                            if(object.selected){
                                idxList.push(idx);
                            }
                        });
                        
                        for(var i = idxList.length-1; i >= 0; i--){
                            this.elementsAvalibles[idxList[i]].selected = false;
                            this.selectedItems.unshift(this.elementsAvalibles[idxList[i]]);
                            this.elementsAvalibles.splice(idxList[i], 1);
                        }

                        break;
                    case 'addAll':
                        this.selectedItems.unshift(...this.elementsAvalibles);
                        this.elementsAvalibles = [];
                        break;
                    case 'rmSimp':
                        var idxList = [];

                        this.selectedItems.forEach((object, idx) => {
                            if(object.selected){
                                idxList.push(idx);
                            }
                        });

                        for(var i = idxList.length -1; i >= 0; i--){
                            this.selectedItems[idxList[i]].selected = false;
                            this.elementsAvalibles.unshift(this.selectedItems[idxList[i]]);
                            this.selectedItems.splice(idxList[i], 1);
                        }
                        break;
                    case 'rmAll':
                        this.elementsAvalibles.unshift(...this.selectedItems);
                        this.selectedItems = [];
                        break;
                    case 'up':
                        var idxList = [];
                        debugger;
                        this.selectedItems.forEach((object, idx) => {
                            if(object.selected && idx > 0){
                                idxList.push(idx);
                            }else if(object.selected && idx == 0){
                                object.selected = false;
                            }
                        });

                        var jumps = 1;
                        for(var i = 0; i < idxList.length; i++){
                            var object = this.selectedItems.splice([idxList[i]], 1);

                            this.selectedItems.splice(idxList[i] - jumps, 0, object[0]);
                            jumps++;
                        }
                        break;
                    case 'down':
                        var idxList = [];

                        this.selectedItems.forEach((object, idx) => {
                            if(object.selected && idx < this.selectedItems.length - 1){
                                idxList.push(idx);
                            }else if(object.selected && idx == this.selectedItems.length - 1){
                                object.selected = false;
                            }
                        });

                        
                        for(var i = idxList.length -1; i >= 0; i--){
                            var object = this.selectedItems.splice([idxList[i]], 1);

                            this.selectedItems.splice(idxList[i] + 1, 0, object[0]);
                        }
                        break;
                }
            },
            /**
             * marca el elemento seleccionado en la interfaz para realizar una accion sobre este en la tabla de elementos disponibles
             * @param id unico del elemento seleccionado
             */
            selectItemAvalibles(id){
                var idx = this.elementsAvalibles.findIndex((x) => x.id == id);
                if(idx >= 0){
                    this.elementsAvalibles[idx].selected = !this.elementsAvalibles[idx].selected;
                }
                console.dir(this.elementsAvalibles[idx]);
            },
            /**
             * * marca el elemento seleccionado en la interfaz para realizar una accion sobre este en la tabla de elementos agregados
             * @param id unico del elemento seleccionado
             */
            selectItemAdded(id){
                var idx = this.selectedItems.findIndex((x) => x.id == id);
                if(idx >= 0){
                    //this.$set(this.lotes_subasta[idx], 'selected', !this.lotes_subasta[idx].selected);
                    this.selectedItems[idx].selected = !this.selectedItems[idx].selected;
                }
            }
        }
    }
</script>

<style>
    .dual-list-container {
        display: flex;
        min-width: 800x;
    }

    .list-container {
		margin-right: 20px;
		vertical-align: top;
        display: flex;
        max-height: 600px;
        flex-direction: column;
        min-height: 600px;
        width: 100%;
	}

	button {
		display: block;
		margin-top: 10px;
	}

	/* Estilos para dar apariencia de tarjeta a las etiquetas h3 */
	.card-title {
		background-color: #f5f5f5; /* Fondo de la tarjeta */
		border: 1px solid #ddd;    /* Borde de la tarjeta */
		padding: 10px;             /* Espaciado interno */
		border-radius: 8px;        /* Bordes redondeados */
		box-shadow: 0 2px 5px rgba(0, 0, 0, 0.15); /* Sombra para la tarjeta */
		margin-bottom: 20px;       /* Espacio inferior */
		text-align: center !important;
		display: flex;
		justify-content: center;
		align-content: center;
		flex-wrap: wrap;
	}

	.list-container-buttons {
		display: inline-block;
		margin-right: 20px;
		vertical-align: top;
        margin-left: 10px;
	}

	.list-container-buttons div{
        display: flex;               /* Habilitar Flexbox */
        justify-content: center;     /* Centrar horizontalmente */
        align-items: center;         /* Centrar verticalmente */
        /*height: 70%;               /* Altura del contenedor */
        flex-direction: column;
	}

	.list-container-buttons div button{
		max-width: 53px;
		animation: pulse 1.5s infinite; /* Animación de pulsación */
	}

	.list-container-buttons div button:hover {
	    transform: scale(1.05); /* Incremento en el tamaño al pasar el cursor */
	}

	/* Animación de pulsación */
	@keyframes pulse {
        0% {
            box-shadow: 0 0 10px rgba(255, 235, 59, 0.3);
        }
        50% {
            box-shadow: 0 0 20px rgba(255, 235, 59, 0.6);
        }
        100% {
            box-shadow: 0 0 10px rgba(255, 235, 59, 0.3);
        }
	}

	.selectable-list {
        display: flex;
        flex-direction: column;
        border: 2px solid #8b8b8b;
        border-radius: 8px;
        padding: 10px;
        background-color: #f9f9f9;
        min-height: 600px;
        overflow-y: auto;
	}

	.selectable-item-selected {
		background-color: #98f3ff !important;
	}

	.selectable-item {
		padding: 10px;
		margin-bottom: 5px;
		background-color: #fff;
		border-radius: 6px;
		cursor: pointer;
		transition: background-color 0.3s, transform 0.2s;
		display: flex;
		flex-direction: row;
		column-gap: 20px;
		font-weight: bold;
		/*height: 60px;
		max-height: 60px;*/
	}

	.selectable-item:hover {
		background-color: #e0f7fa; /* Fondo claro al pasar el cursor */
	}
</style>