<template>
    <header>
        <h1 class="page-title">Документы</h1>
        <div class="page-actions">
            <div class="action-bookmark">
                <img src="../public/img/header/Vector.png" />
            </div>
            <div class="action-new-category">
                <img src="../public/img/header/Add.png" />
                Новый тип
            </div>
            <div class="action-new-element">
                <img src="../public/img/header/Add.png" />
                Новый документ
            </div>
        </div>
    </header>
    <main>
        <div class="search-bar">
            <input
                @focus="inputFocus = true"
                @blur="inputFocus = false"
                class="search"
                :class="inputFocus ? 'search-focus' : ''"
                v-model="searchQuery"
                type="text"
                placeholder="Поиск"
            />
            <button @click="clickClose()" class="search-close">
                <img
                    v-if="!searchQuery"
                    class="search-close-img"
                    src="../public/img/search/close.png"
                />
                <img v-else src="../public/img/search/close.png" />
            </button>
        </div>

        <ul class="category-list">
            <!-- <button @click="searchQuery = ''">
                Проверка метода searchedCategory
            </button>
            <button @click="searchElem()">
                Проверка метода searchedElement
            </button> -->
            <template v-for="(category, categoryIndex) in filteredCategories">
                <li>
                    <div class="category-container-bord">
                        <div
                            class="category-container"
                            :draggable="category.dragging"
                            @dragstart="onDragStart($event, categoryIndex, -1)"
                            @drop="onDrop($event, categoryIndex, -1)"
                            @dragover.prevent
                            @dragenter="category.droppable = true"
                            @dragleave="category.droppable = false"
                            @dragend="dragEnd"
                            :class="[
                                category.droppable ? 'drop' : '',
                                category.dragging
                                    ? 'drag-color-none'
                                    : 'drag-color-return',
                            ]"
                        >
                            <div class="category">
                                <button
                                    class="collapse"
                                    @click="
                                        category.collapse = category.elements
                                            .length
                                            ? !category.collapse
                                            : category.collapse
                                    "
                                >
                                    <div
                                        :class="
                                            category.collapse
                                                ? 'category-button-down'
                                                : 'category-button-up'
                                        "
                                    ></div>
                                </button>
                                <div>
                                    {{ category.title }}
                                    <span
                                        v-for="color in category.colors"
                                        class="dot"
                                        :style="{ color: color }"
                                        >&#11044;</span
                                    >
                                    <span class="description">{{
                                        category.description
                                    }}</span>
                                </div>
                            </div>
                            <div class="action-buttons">
                                <button class="category-button-edit"></button>
                                <button
                                    v-if="category.elements != 0"
                                    :class="'category-delete-disabled'"
                                ></button>
                                <button
                                    v-else
                                    :class="'category-delete-active'"
                                ></button>
                                <button
                                    :class="
                                        category.highlightedArrow
                                            ? 'category-button-move-blue'
                                            : 'category-button-move'
                                    "
                                    @mouseover="
                                        category.highlightedArrow = true
                                    "
                                    @mouseout="
                                        category.highlightedArrow = false
                                    "
                                    @mousedown="category.dragging = true"
                                    @mouseup="category.dragging = false"
                                ></button>
                            </div>
                        </div>

                        <div v-if="category.dragging" class="dragImg">
                            <div class="category-container drag-clone">
                                <div class="category">
                                    <button
                                        class="collapse"
                                        @click="
                                            category.collapse = category
                                                .elements.length
                                                ? !category.collapse
                                                : category.collapse
                                        "
                                    >
                                        <div
                                            :class="
                                                category.collapse
                                                    ? 'category-button-down'
                                                    : 'category-button-up'
                                            "
                                        ></div>
                                    </button>
                                    <div>
                                        {{ category.title }}
                                        <span
                                            v-for="color in category.colors"
                                            class="dot"
                                            :style="{ color: color }"
                                            >&#11044;</span
                                        >
                                        <span class="description">{{
                                            category.description
                                        }}</span>
                                    </div>
                                </div>
                                <div class="action-buttons">
                                    <button
                                        class="category-button-edit"
                                    ></button>
                                    <button
                                        v-if="category.elements != 0"
                                        :class="'category-delete-disabled'"
                                    ></button>
                                    <button
                                        v-else
                                        :class="'category-delete-active'"
                                    ></button>
                                    <button
                                        :class="
                                            category.highlightedArrow
                                                ? 'category-button-move-blue'
                                                : 'category-button-move'
                                        "
                                    ></button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <ul class="elem-list">
                        <template
                            v-for="(element, elementIndex) in category.elements"
                        >
                            <li
                                :class="
                                    !category.collapse
                                        ? 'elem-open'
                                        : 'elem-close'
                                "
                            >
                                <div
                                    class="element-container"
                                    :draggable="element.dragging"
                                    @dragstart="
                                        onDragStart(
                                            $event,
                                            categoryIndex,
                                            elementIndex
                                        )
                                    "
                                    @drop="
                                        onDrop(
                                            $event,
                                            categoryIndex,
                                            elementIndex
                                        )
                                    "
                                    @dragover.prevent
                                    @dragenter="element.droppable = true"
                                    @dragleave="element.droppable = false"
                                    @dragend="dragEnd"
                                    :class="[
                                        element.droppable ? 'drop' : '',
                                        element.dragging
                                            ? 'drag-color-none'
                                            : 'drag-color-return',
                                    ]"
                                >
                                    <div>
                                        {{ element.title }}
                                        <span
                                            v-for="color in element.colors"
                                            class="dot"
                                            :style="{ color: color }"
                                        >
                                            &#11044;</span
                                        >
                                        <span
                                            v-if="element.required"
                                            class="required"
                                            >Обязательный</span
                                        >
                                        <span class="description">{{
                                            element.description
                                        }}</span>
                                    </div>
                                    <div class="action-buttons">
                                        <button
                                            class="category-button-edit"
                                        ></button>
                                        <button
                                            class="category-delete-active"
                                        ></button>
                                        <button
                                            :class="
                                                element.highlightedArrow
                                                    ? 'category-button-move-blue'
                                                    : 'category-button-move'
                                            "
                                            @mouseover="
                                                element.highlightedArrow = true
                                            "
                                            @mouseout="
                                                element.highlightedArrow = false
                                            "
                                            @mousedown="element.dragging = true"
                                            @mouseup="element.dragging = false"
                                        ></button>
                                    </div>
                                </div>
                                <div v-if="element.dragging" class="dragImg">
                                    <div class="element-container drag-clone">
                                        <div>
                                            {{ element.title }}
                                            <span
                                                v-for="color in element.colors"
                                                class="dot"
                                                :style="{ color: color }"
                                            >
                                                &#11044;</span
                                            >
                                            <span
                                                v-if="element.required"
                                                class="required"
                                                >Обязательный</span
                                            >
                                            <span class="description">{{
                                                element.description
                                            }}</span>
                                        </div>
                                        <div class="action-buttons">
                                            <button
                                                class="category-button-edit"
                                            ></button>
                                            <button
                                                class="category-delete-active"
                                            ></button>
                                            <button
                                                :class="
                                                    element.highlightedArrow
                                                        ? 'category-button-move-blue'
                                                        : 'category-button-move'
                                                "
                                            ></button>
                                        </div>
                                    </div>
                                </div>
                            </li>
                        </template>
                    </ul>
                </li>
            </template>
        </ul>

        <ul class="additional-list">
            <template v-for="(element, elementIndex) in filteredElements">
                <li>
                    <div
                        class="element-container"
                        :draggable="element.dragging"
                        @dragstart="onDragStart($event, -1, elementIndex)"
                        @drop="onDrop($event, -1, elementIndex)"
                        @dragover.prevent
                        @dragenter="element.droppable = true"
                        @dragleave="element.droppable = false"
                        @dragend="dragEnd"
                        :class="[
                            element.droppable ? 'drop' : '',
                            element.dragging
                                ? 'drag-color-none'
                                : 'drag-color-return',
                        ]"
                    >
                        <div>
                            {{ element.title }}
                            <span
                                v-for="color in element.colors"
                                class="dot"
                                :style="{ color: color }"
                                >&#11044;</span
                            >
                            <span class="description">{{
                                element.description
                            }}</span>
                        </div>
                        <div class="action-buttons">
                            <button class="category-button-edit"></button>
                            <button class="category-delete-active"></button>
                            <button
                                :class="
                                    element.highlightedArrow
                                        ? 'category-button-move-blue'
                                        : 'category-button-move'
                                "
                                @mouseover="element.highlightedArrow = true"
                                @mouseout="element.highlightedArrow = false"
                                @mousedown="element.dragging = true"
                                @mouseup="element.dragging = false"
                            ></button>
                        </div>
                    </div>
                    <div v-if="element.dragging" class="dragImg">
                        <div class="element-container drag-clone">
                            <div>
                                {{ element.title }}
                                <span
                                    v-for="color in element.colors"
                                    class="dot"
                                    :style="{ color: color }"
                                    >&#11044;</span
                                >
                                <span class="description">{{
                                    element.description
                                }}</span>
                            </div>
                            <div class="action-buttons">
                                <button class="category-button-edit"></button>
                                <button class="category-delete-active"></button>
                                <button
                                    :class="
                                        element.highlightedArrow
                                            ? 'category-button-move-blue'
                                            : 'category-button-move'
                                    "
                                ></button>
                            </div>
                        </div>
                    </div>
                    <!-- <div class="element-dropzone"></div> -->
                </li>
            </template>
        </ul>
    </main>
</template>

<script>
import { reactive } from 'vue';

export default {
    data() {
        return {
            searchQuery: '',
            inputFocus: false,
            console: console,
            categories: [
                {
                    title: 'Обязательные для всех',
                    colors: ['#ff238d', '#ffb800', '#ff8d23'],
                    description:
                        'Документы, обязательные для всех сотрудников без исключения',
                    collapse: false,
                    dragging: false,
                    highlightedArrow: false,
                    droppable: false,
                    elements: [
                        {
                            title: 'Паспорт',
                            colors: ['#00c2ff'],
                            required: true,
                            description: 'Для всех',
                            dragging: false,
                            highlightedArrow: false,
                            droppable: false,
                        },
                        {
                            title: 'ИНН',
                            colors: [],
                            required: true,
                            description: 'Для всех',
                            dragging: false,
                            highlightedArrow: false,
                            droppable: false,
                        },
                    ],
                },
                {
                    title: 'Обязательные для трудоустройства',
                    colors: [],
                    description:
                        'Документы, без которых невозможно трудоустройство человека на какую бы то ни было должность в компании вне зависимости от граж',
                    collapse: false,
                    dragging: false,
                    highlightedArrow: false,
                    droppable: false,
                    elements: [
                        {
                            title: 'Опция 1',
                            colors: [],
                            required: false,
                            description: 'Для всех',
                            dragging: false,
                            highlightedArrow: false,
                            droppable: false,
                        },
                        {
                            title: 'Опция 2',
                            colors: [],
                            required: false,
                            description: 'Для всех',
                            dragging: false,
                            highlightedArrow: false,
                            droppable: false,
                        },
                    ],
                },
                {
                    title: 'Специальные',
                    colors: [],
                    description: '',
                    collapse: false,
                    dragging: false,
                    highlightedArrow: false,
                    droppable: false,
                    elements: [
                        {
                            title: 'Опция 3',
                            colors: [],
                            required: false,
                            description: 'Для всех',
                            dragging: false,
                            highlightedArrow: false,
                            droppable: false,
                        },
                        {
                            title: 'Опция 4',
                            colors: [],
                            required: false,
                            description: 'Для всех',
                            dragging: false,
                            highlightedArrow: false,
                            droppable: false,
                        },
                    ],
                },
            ],
            elements: [
                {
                    title: 'Тестовое задание',
                    colors: [],
                    required: false,
                    description:
                        'Россия, Белоруссия, Украина, администратор филиала, повар-сушист, повар-пиццмейкер, повар горячего цеха',
                    dragging: false,
                    highlightedArrow: false,
                    droppable: false,
                },
                {
                    title: 'Трудовой договор',
                    colors: ['#0066ff', '#8e9cbb'],
                    required: false,
                    description: '',
                    dragging: false,
                    highlightedArrow: false,
                    droppable: false,
                },
                {
                    title: 'Мед книжка',
                    colors: [],
                    required: false,
                    description: '',
                    dragging: false,
                    highlightedArrow: false,
                    droppable: false,
                },
            ],
        };
    },
    computed: {
        filteredCategories() {
            if (!this.searchQuery) {
                return this.categories;
            }

            let filtered = [];

            for (let category of this.categories) {
                if (
                    category.title
                        .toLowerCase()
                        .includes(this.searchQuery.toLowerCase())
                ) {
                    filtered.push(category);
                } else {
                    let elements = category.elements.filter((elem) =>
                        elem.title
                            .toLowerCase()
                            .includes(this.searchQuery.toLowerCase())
                    );
                    if (elements.length) {
                        let newCategory = reactive(category);

                        newCategory.elements = elements;

                        filtered.push(newCategory);
                    }
                }
            }

            return filtered;
        },
        filteredElements() {
            return this.elements.filter((elem) =>
                elem.title
                    .toLowerCase()
                    .includes(this.searchQuery.toLowerCase())
            );
        },
    },
    methods: {
        // addColor(event) {
        //     event.target.classList.add('elem-with-shadow');
        // },
        addClassDrop(event) {
            // this.console.log('dragover');
            event.preventDefault();
            this.classList.add('elem-with-shadow');
        },
        onDragStart(event, categoryIndex, elementIndex) {
            event.dataTransfer.dropEffect = 'move';
            event.dataTransfer.effectAllowed = 'move';

            event.dataTransfer.setData('fromCategoryIndex', categoryIndex);

            event.dataTransfer.setData('fromElementIndex', elementIndex);

            let dragClone = document.querySelector('.dragImg');
            // let dragClone = event.target.parentNode.children[1];
            // this.console.log(dragClone);

            event.dataTransfer.setDragImage(dragClone, 1150, 24);

            return true;
        },
        onDrop(event, categoryIndex, elementIndex) {
            const fromCategoryIndex =
                event.dataTransfer.getData('fromCategoryIndex');
            console.log(fromCategoryIndex);

            const fromElementIndex =
                event.dataTransfer.getData('fromElementIndex');
            console.log(fromElementIndex);

            const toCategoryIndex = categoryIndex;
            console.log(toCategoryIndex);

            const toElementIndex = elementIndex;
            console.log(toElementIndex);

            // тащим категорию в additional list
            if (fromElementIndex == -1 && toCategoryIndex == -1) {
                this.filteredCategories[fromCategoryIndex].dragging = false;
                this.filteredElements[toElementIndex].droppable = false;
                return false;
                // тащим категорию...
            } else if (
                fromElementIndex == -1 &&
                toCategoryIndex !== -1 &&
                toElementIndex !== -1
            ) {
                this.filteredCategories[fromCategoryIndex].dragging = false;
                this.filteredCategories[toCategoryIndex].elements[
                    toElementIndex
                ].droppable = false;
                return false;
            } else if (fromElementIndex == -1) {
                // ... внутри category list
                if (toCategoryIndex !== -1) {
                    this.filteredCategories[toCategoryIndex].droppable = false;
                }

                this.moveCategories(fromCategoryIndex, toCategoryIndex);
            }
            // fromElementIndex не равен -1, следовательно тащим елемент
            else {
                /*
                    1) можем тащить в additional list (на элемент) => toCategoryIndex == -1 and toElementIndex !== -1 : убираем подсветку у filteredElements
                    2) можем в elem list (на элемент) => toCategoryIndex !== -1 and toElementIndex !== -1 : убираем подсветку у filteredCategories.elements
                    3) можем в category list (на категорию) => toCategoryIndex !== -1 and toElementIndex == -1 : убираем подсветку у filteredCategories
                */

                if (toCategoryIndex !== -1 && toElementIndex == -1) {
                    // this.filteredCategories[toCategoryIndex].elements[
                    //     toElementIndex
                    // ].droppable = false;
                    // this.filteredElements[toElementIndex].droppable = false;
                    this.filteredCategories[toCategoryIndex].droppable = false;
                    this.console.log('tyt');
                } else if (toCategoryIndex == -1 && toElementIndex == -1) {
                    this.filteredElements[toElementIndex].droppable = false;
                    this.console.log('tyt2');
                } else if (toCategoryIndex == -1 && toElementIndex !== -1) {
                    this.filteredElements[toElementIndex].droppable = false;
                    this.console.log('tyt2');
                } else if (toCategoryIndex !== -1 && toElementIndex !== -1) {
                    this.console.log(toCategoryIndex);
                    this.console.log(toElementIndex);
                    this.filteredCategories[toCategoryIndex].elements[
                        toElementIndex
                    ].droppable = false;
                } else if (toCategoryIndex !== -1 && toElementIndex == -1) {
                    this.filteredCategories[toCategoryIndex].elements[
                        toElementIndex
                    ].droppable = false;
                }

                this.moveElement(
                    fromCategoryIndex,
                    fromElementIndex,
                    toCategoryIndex,
                    toElementIndex
                );
            }

            // event.stopPropagation();
            // return false;
        },
        moveElement(
            fromCategoryIndex,
            fromElementIndex,
            toCategoryIndex,
            toElementIndex
        ) {
            let element;

            if (fromCategoryIndex == -1) {
                element = this.elements[fromElementIndex];
                element.dragging = false;

                this.elements = this.elements.filter((elem) => elem != element);
            } else if (fromCategoryIndex >= 0) {
                element =
                    this.categories[fromCategoryIndex].elements[
                        fromElementIndex
                    ];
                element.dragging = false;
                this.categories[fromCategoryIndex].elements = this.categories[
                    fromCategoryIndex
                ].elements.filter((elem) => elem != element);
            }

            if (toCategoryIndex == -1) {
                if (toElementIndex == -1) {
                    let newElements = this.elements.unshift(element);
                } else {
                    let newElements = this.elements.splice(
                        toElementIndex + 1,
                        0,
                        element
                    );
                }
            } else {
                if (toElementIndex == -1) {
                    let newElements =
                        this.categories[toCategoryIndex].elements.unshift(
                            element
                        );
                } else {
                    // this.console.log(
                    //     this.categories[toCategoryIndex].elements[
                    //         toElementIndex
                    //     ]
                    // );
                    let newElements = this.categories[
                        toCategoryIndex
                    ].elements.splice(toElementIndex + 1, 0, element);
                }
            }
        },
        moveCategories(fromCategoryIndex, toCategoryIndex) {
            let categories = [];
            let category = this.categories[fromCategoryIndex];

            for (let i = 0; i < this.categories.length; i++) {
                if (i == fromCategoryIndex) {
                    continue;
                } else if (i == toCategoryIndex) {
                    categories.push(this.categories[i]);
                    this.categories[i].dragging = false;
                    categories.push(category);
                    category.dragging = false;
                } else {
                    categories.push(this.categories[i]);
                    this.categories[i].dragging = false;
                }
            }

            this.categories = categories;
        },
        handleDragEnter(e) {
            this.droppable = true;
        },
        dragEnd(event) {
            event.target.dragging = false;
            // event.target.style.opacity = '1';
            this.console.log(event.target.dragging);
        },
        clickClose() {
            this.searchQuery = '';
            this.inputFocus = false;
        },
    },
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fira+Sans:ital,wght@0,400;0,500;1,400&family=Roboto&family=Roboto+Flex:opsz,wght@8..144,500;8..144,600&family=Roboto+Serif:opsz,wght@8..144,500&family=Roboto+Slab:wght@500&display=swap');

*,
*::after,
*::before {
    box-sizing: border-box;
}

header {
    display: flex;
    justify-content: space-between;
    width: 100%;
    min-width: 1860px;
    height: 90px;
    padding: 38px 30px 23px 30px;
    /* border: solid 1px red; */
}
.page-actions {
    display: flex;
    gap: 5px;
}
.page-title {
    font-family: 'Fira Sans';
    font-style: normal;
    font-weight: 500;
    font-size: 22px;
    color: #000000;
    /* border: solid 1px red; */
}
.action-bookmark {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 30px;
    height: 30px;
    border: 1px solid #d3d8df;
    border-radius: 50px;
}
.action-bookmark img {
    width: 12px;
    height: 15px;
}
.action-new-category {
    display: flex;
    align-items: center;
    margin-left: 10px;
    width: 115px;
    height: 30px;
    border: 1px solid #d3d8df;
    border-radius: 50px;
    font-family: 'Fira Sans';
    font-weight: 500;
    font-size: 12px;
    color: #000000;
}

.action-new-element {
    display: flex;
    align-items: center;
    width: 149px;
    height: 30px;
    border: 1px solid #d3d8df;
    border-radius: 50px;
    font-family: 'Fira Sans';
    font-weight: 500;
    font-size: 12px;
    color: #000000;
}

.action-new-category img,
.action-new-element img {
    width: 14px;
    height: 14px;
    margin: 0 10px;
}

main {
    width: 1190px;
    min-height: 600px;
    padding: 0 30px;
    /* border: solid 1px green; */
}

.search-bar {
    display: flex;
    justify-content: space-between;
    width: 564px;
}

.search {
    width: 564px;
    height: 30px;
    border: none;
    outline: none;
    border-bottom: solid 1px #8e9cbb;
    font-family: 'Fira Sans';
    font-weight: 400;
    font-size: 15px;
    background: url(../public/img/search/search.png) no-repeat left;
    background-position: left top;
    padding-left: 30px;
    padding-bottom: 12px;
    margin-bottom: 23px;
}

.search-focus {
    border-bottom: solid 1px #0066ff;
}

.search-close {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 20px;
    width: 3px;
    text-align: center;
    align-items: center;
    background-color: #fff;
    border: none;
    position: relative;
    left: -15px;
}

.search-close-img {
    display: none;
}

#search::placeholder {
    font-family: 'Fira Sans';
    font-style: italic;
    font-weight: 400;
    font-size: 15px;
    color: #8e9cbb;
}

li {
    list-style-type: none;
}

.category-list {
    /* background-color: #fff;
    /* padding: 0; */
    /* border: solid 1px red; */
}

ul {
    padding: 0;
}

li + li {
    margin: -1px 0;
}

.category-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 1190px;
    padding-bottom: 6px;
    font-family: 'Fira Sans';
    font-weight: 500;
    font-size: 15px;
    height: 48px;
    background-color: #fff;
    border: 1px solid #dfe4ef;
}

.category-container-bord {
    border-bottom: 1px solid #dfe4ef;
}

.category {
    /* position: relative;
    top: 25px;
    left: 30px; */
    display: flex;
    gap: 14px;
    font-family: 'Fira Sans';
    font-weight: 500;
    font-size: 15px;
    color: #000000;
    padding-left: 17px;
    /* border: 1px solid #ff238d; */
}

.category > .collapse {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 22px;
    height: 22px;
    background-color: #fff;
    border: 1px solid #dfe4ef;
    border-radius: 50%;
}

/* .category > button img {
    width: 8px;
    height: 5px;
} */

.category-button-up {
    width: 8px;
    height: 5px;
    background: url(../public/img/category/category-arrow-up.png) no-repeat;
}

.category-button-down {
    width: 8px;
    height: 5px;
    background: url(../public/img/category/category-arrow-down.png) no-repeat;
}

.element-dropzone {
    position: relative;
    z-index: 5;
    /* top: -33px; */
    width: 1190px;
    height: 5px;
    border: solid 2px rebeccapurple;
}

.action-buttons {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100%;
    /* background-color: #fff; */
}

.action-buttons > button {
    align-items: center;
    height: 30px;
    width: 32px;
    /* border: solid 1px red; */
    text-align: center;
    background-position: center;
    border: none;
}
.category-container .action-buttons > button:last-child {
    height: 48px;
}
.category-button-edit {
    background: url(../public/img/category/category-pen.png) no-repeat;
}

.category-delete-active {
    background: url(../public/img/category/category-delete.png) no-repeat;
}

.category-delete-disabled {
    background: url(../public/img/category/deletetrash.png) no-repeat;
}

.category-button-move {
    height: 15px;
    width: 14px;
    background: url(../public/img/category/category-arrow-move.png) no-repeat;
}

.category-button-move-blue {
    height: 15px;
    width: 14px;
    background: url(../public/img/category/category-move-blue.png) no-repeat;
}
.additional-list {
    /* position: relative; */
    width: 1190px;
    margin-top: 14px;
    background-color: #fff;
    /* border: solid 1px red; */
    /* border: 1px solid #dfe4ef; */
    /* padding: 0; */
}

.elem-list {
    width: 1190px;
    background-color: #fff;
    /* border: 1px solid #dfe4ef; */
    /* padding: 0; */
}

.elem-list > li,
.additional-list > li {
    /* display: flex;
    justify-content: space-between;
    align-items: center; */
    /* z-index: 10; */
    /* position: relative; */
    width: 1190px;
    height: 34px;
    font-family: 'Fira Sans';
    font-weight: 400;
    font-size: 13px;
    padding-bottom: 6px;
    margin: -0.5px 0;
    color: #000000;
    border: 1px solid #dfe4ef;
}

.element-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.element-container > div {
    padding-left: 17px;
}

.elem-list > li {
    margin-left: 16px;
    width: 1174px;
}

.elem-open {
    padding: 0;
    margin: 0;
    list-style-type: none;
    /* overflow: hidden; */
    /* max-height: 500px; */
    transition: max-height 1s 0s ease-in;
}

.elem-close {
    overflow: hidden;
    max-height: 0px;
    border: none !important;
    transition: max-height 0.5s -0.3s ease-out;
}

.elem-with-shadow {
    box-shadow: 0px 3px 16px rgba(0, 102, 255, 0.7);
}

.required {
    font-family: 'Fira Sans';
    font-weight: 400;
    font-size: 11px;
    margin-left: 15px;
    color: #ff238d;
}

.description {
    font-family: 'Fira Sans';
    font-weight: 400;
    font-size: 11px;
    margin-left: 15px;
    color: #8e9cbb;
}

.dot {
    position: relative;
    top: -2px;
    font-size: 7px;
    color: #ff238d;
    margin: 0 2px;
}
li:first-child .dot:first-of-type {
    margin-left: 14px;
}

.drop {
    border-bottom: solid 4px #0066ff;
}

.drag-clone {
    width: 1160px;
    height: 35px;
    background: #ffffff;
    border: 1px solid #dfe4ef;
    box-shadow: 0px 3px 16px rgba(0, 102, 255, 0.7);
}

.dragImg {
    position: absolute;
    padding: 7px;
    top: -3271px;
}

.drag-color-none {
    opacity: 0.4;
}
.drag-color-return {
    opacity: 1;
}
</style>
