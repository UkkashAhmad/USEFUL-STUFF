<script setup>
import { Undo2, Redo2, Bold, ChevronDown, Italic, Code, Strikethrough, List, ListOrdered, Image, Quote, TableProperties, Rows2, Eraser } from 'lucide-vue-next'
import { Editor, EditorContent } from "@tiptap/vue-3";
import StarterKit from "@tiptap/starter-kit";
import TextStyle from "@tiptap/extension-text-style";
import Color from "@tiptap/extension-color";
import FontFamily from "@tiptap/extension-font-family";
import ImageResize from "tiptap-extension-resize-image";
import Table from "@tiptap/extension-table";
import TableCell from "@tiptap/extension-table-cell";
import TableHeader from "@tiptap/extension-table-header";
import TableRow from "@tiptap/extension-table-row";
import BaseDropdown from '../dropdown/BaseDropdown.vue';
import BaseCustomGrid from './BaseCustomGrid.vue';
//======================= variables ==========================
const props = defineProps({
  modelValue: String,
  label: {
    type: String,
    default: "",
  },
  name: {
    type: String,
    default: "editor",
  },
  label: {
    type: String,
    default: "",
  },
  rules: {
    type: String,
    default: "",
  },
  height: {
    type: String,
    default: "150"
  }
});
const editor = ref(null);
const selectedFont = ref("Arial");
const fontSize = [
  { value: 0, label: "Paragraph" },
  { value: 1, label: "Heading 1" },
  { value: 2, label: "Heading 2" },
  { value: 3, label: "Heading 3" },
  { value: 4, label: "Heading 4" },
  { value: 5, label: "Heading 5" },
  { value: 6, label: "Heading 6" },
];
const currentHeading = computed(() => {
  return [1,2,3,4,5,6].find(level =>
    editor.value.isActive('heading', { level })
  ) || null
})
const fontFamilies = [
  { label: "Archivo", value: "Archivo", fontFamily: "Archivo" },
  { label: "Arial", value: "Arial", fontFamily: "Arial" },
  { label: "Courier New", value: "Courier New", fontFamily: "Courier New" },
  { label: "Georgia", value: "Georgia", fontFamily: "Georgia" },
  {
    label: "Times New Roman",
    value: "Times New Roman",
    fontFamily: "Times New Roman",
  },
  { label: "Verdana", value: "Verdana", fontFamily: "Verdana" },
  { label: "Roboto", value: "Roboto", fontFamily: "Roboto" },
  { label: "Open Sans", value: "Open Sans", fontFamily: "Open Sans" },
  { label: "Lato", value: "Lato", fontFamily: "Lato" },
  { label: "Montserrat", value: "Montserrat", fontFamily: "Montserrat" },
  { label: "Raleway", value: "Raleway", fontFamily: "Raleway" },
  { label: "Poppins", value: "Poppins", fontFamily: "Poppins" },
  { label: "Noto Sans", value: "Noto Sans", fontFamily: "Noto Sans" },
  {
    label: "Merriweather",
    value: "Merriweather",
    fontFamily: "Merriweather",
  },
  {
    label: "Playfair Display",
    value: "Playfair Display",
    fontFamily: "Playfair Display",
  },
  { label: "Ubuntu", value: "Ubuntu", fontFamily: "Ubuntu" },
  { label: "Oswald", value: "Oswald", fontFamily: "Oswald" },
  { label: "Nunito", value: "Nunito", fontFamily: "Nunito" },
  { label: "PT Serif", value: "PT Serif", fontFamily: "PT Serif" },
  {
    label: "Dancing Script",
    value: "Dancing Script",
    fontFamily: "Dancing Script",
  },
  { label: "Quicksand", value: "Quicksand", fontFamily: "Quicksand" },
  { label: "Rubik", value: "Rubik", fontFamily: "Rubik" },
  { label: "Pacifico", value: "Pacifico", fontFamily: "Pacifico" },
  {
    label: "Indie Flower",
    value: "Indie Flower",
    fontFamily: "Indie Flower",
  },
];
const applyFontFamily = () => {
  if (selectedFont.value) {
    editor.value.chain().focus().setFontFamily(selectedFont.value).run();
  } else {
    editor.value.chain().focus().unsetFontFamily().run(); // Reset to default if empty
  }
};
const applyFontWeight = (val, closeDropdown) => {
  
  if (val && val !== 0) {
    editor.value.chain().focus().toggleHeading({ level: val }).run();
  } else {
    editor.value.chain().focus().setParagraph().run();
  }
  closeDropdown()
};
const onImageUpload = (event) => {
  const file = event.target.files[0];

  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      const url = e.target.result; // Get image URL from file reader
      editor.value
        .chain()
        .focus()
        .setImage({ src: url, width: 400, height: 400 })
        .run(); // Insert image into the editor
    };
    reader.readAsDataURL(file); // Read the image file as a data URL
  }
};
// =======================================Validations =========================
// defineRule("contentRequired", (value) => {
//   if (!props.modelValue || props.modelValue.length < 8) {
//     return (props.label || "Editor Content") + " is required";
//   }
//   return true;
// });
// const { errorMessage, validate: validateEditor } = useField(
//   "editorContent",
//   "contentRequired"
// );

// watch(
//   () => props.modelValue,
//   () => {
//     validateEditor(); // Trigger validation on content change
//   }
// );
//========================= Table section =====================
const tableActions = [
  { title: "Column Actions" },
  { label: "Add column before", value: "addColumnBefore" },
  { label: "Add column after", value: "addColumnAfter" },
  { label: "Toggle header column", value: "toggleHeaderColumn" },
  { label: "Delete column", value: "deleteColumn" },
  { title: "Row Actions" },
  { label: "Add row before", value: "addRowBefore" },
  { label: "Add row after", value: "addRowAfter" },
  { label: "Toggle header row", value: "toggleHeaderRow" },
  { label: "Toggle header cell", value: "toggleHeaderCell" },
  { label: "Delete row", value: "deleteRow" },
  { title: "Table Actions" },
  { label: "Delete table", value: "deleteTable" },
  { label: "Merge cells", value: "mergeCells" },
  { label: "Split cell", value: "splitCell" },
  // { label: "Merge or split", value: "mergeOrSplit" },
  // { label: "Set cell attribute", value: "setCellAttribute" },
  { label: "Fix tables", value: "fixTables" },
];

function handleTableAction(rf, val) {
  const editorContent = editor.value.chain().focus();

  switch (rf) {
    case "insertTable":
      editor.value
        .chain()
        .focus()
        .insertTable({ rows: val.rows, cols: val.cols, withHeaderRow: true })
        .run();
      break;
    case "addColumnBefore":
      editorContent.addColumnBefore().run();
      break;
    case "addColumnAfter":
      editorContent.addColumnAfter().run();
      break;
    case "deleteColumn":
      editorContent.deleteColumn().run();
      break;
    case "addRowBefore":
      editorContent.addRowBefore().run();
      break;
    case "addRowAfter":
      editorContent.addRowAfter().run();
      break;
    case "deleteRow":
      editorContent.deleteRow().run();
      break;
    case "deleteTable":
      editorContent.deleteTable().run();
      break;
    case "mergeCells":
      editorContent.mergeCells().run();
      break;
    case "splitCell":
      editorContent.splitCell().run();
      break;
    case "toggleHeaderColumn":
      editorContent.toggleHeaderColumn().run();
      break;
    case "toggleHeaderRow":
      editorContent.toggleHeaderRow().run();
      break;
    case "toggleHeaderCell":
      editorContent.toggleHeaderCell().run();
      break;
    case "mergeOrSplit":
      editorContent.mergeOrSplit().run();
      break;
    case "setCellAttribute":
      editorContent.setCellAttribute("colspan", 2).run();
      break;
    case "fixTables":
      editorContent.fixTables().run();
      break;
    case "goToNextCell":
      editorContent.goToNextCell().run();
      break;
    case "goToPreviousCell":
      editorContent.goToPreviousCell().run();
      break;
    default:
      break;
  }
}
const CustomTableCell = TableCell.extend({
  addAttributes() {
    return {
      // extend the existing attributes …
      ...this.parent?.(),

      // and add a new one …
      backgroundColor: {
        default: null,
        parseHTML: (element) => element.getAttribute("data-background-color"),
        renderHTML: (attributes) => {
          return {
            "data-background-color": attributes.backgroundColor,
            style: `background-color: ${attributes.backgroundColor}`,
          };
        },
      },
    };
  },
});

// Emit event to update parent component
const emit = defineEmits(["update:modelValue"]);

// Local content to handle the editor's value
const localContent = ref(props.modelValue);

// Watch for changes in local content and update parent via emit
watch(localContent, (newValue) => {
  emit("update:modelValue", newValue);
});
watch(
  () => props.modelValue,
  (newValue) => {
    if (editor.value && editor.value.getHTML() !== newValue) {
      editor.value.commands.setContent(newValue);
    }
  }
);

onMounted(() => {
  editor.value = new Editor({
    extensions: [
      StarterKit,
      TextStyle,

      FontFamily.configure({
        types: ["textStyle"],
      }),
      Color.configure({ types: [TextStyle.name] }),
      ImageResize,
      Table.configure({
        resizable: true,
      }),
      TableRow,
      TableHeader,
      CustomTableCell,
    ],

    content: props.modelValue,
    onUpdate({ editor }) {
      emit("update:modelValue", editor.getHTML());
    },
  });
  editor.value.chain().focus().setFontFamily("Arial").run();
  editor.value.on('selectionUpdate', () => {
    const font = editor.value.getAttributes('textStyle')?.fontFamily
    selectedFont.value = font || '' // show actual font under cursor
  })
});

onBeforeUnmount(() => {
  if (editor.value) {
    editor.value.destroy();
  }
});
</script>



<template>
  <div v-if="editor" class="editor-wrapper default-css flex flex-col gap-3 h-full">
    <label
      v-if="label"
      
      class="text-sm  inline-block font-normal"
    >
      <!-- :class="{ '!text-danger': errorMessage }" -->
      {{ label }}
      <span v-if="rules.includes('required')" class="text-red-600 absolute ml-1"
        >*</span
      >
    </label>
    <div
      class="border flex flex-col rounded-sm overflow-hidden h-[60vh]"
    >
      <!-- :class="[{ '!border-danger': errorMessage }, $attrs.class]" -->
      <div class="flex items-center bg-[#F6F5FA] w-[100%]">
        <div class="button-group">
          <!-- new -->
            <!-- :disabled="!editor.can().chain().focus().undo().run()" -->
          <button
            @click="editor.chain().focus().undo().run()"
          >
            <Undo2 class="size-4 text-black" />
          </button>
            <!-- :disabled="!editor.can().chain().focus().redo().run()" -->
          <button
            @click="editor.chain().focus().redo().run()"
          >
            <Redo2 class="size-4 text-black"/>
          </button>
          <select
            v-model="selectedFont"
            class="bg-transparent border-none w-min outline-none text-ellipsis max-w-[6rem] focus:!ring-0"
            @change="applyFontFamily"
          >
            <option
              v-for="font in fontFamilies"
              :key="font.value"
              :value="font.value"
              :style="{ fontFamily: font.fontFamily }"
            >
              {{ font.label }}
            </option>
          </select>
          <span class="text-center">
            <!-- @click="" -->
            <button @click="$refs.fontColor?.click()">
              <!-- <colorPalette class="mb-[-1rem] relative z-40 -right-1 size-3" /> -->

              <input
                ref="fontColor"
                class="size-6"
                type="color"
                :value="editor.getAttributes('textStyle').color || '#000000'"
                @input="editor.chain().focus().setColor($event.target.value).run()"
              />
            </button>
          </span>
          <span class="flex items-center gap-0">
            <button
              :disabled="!editor.can().chain().focus().toggleBold().run()"
              :class="{ 'is-active': editor.isActive('bold') }"
              @click="editor.chain().focus().toggleBold().run()"
            >
              <Bold class="size-4 text-black font-bold"/>
            </button>
            <BaseDropdown position="bottom-center" :overlay="false">
  <template #trigger>
    <div class="flex items-center gap-1">
      <ChevronDown class="size-4 text-black"/>
      <span class="text-xs">H{{ currentHeading ?? '' }}</span> <!-- 👈 show selected -->
    </div>
  </template>

  <template #content="{ closeDropdown }">
    <ul class="!bg-white default-css">
      <li
        v-for="size in fontSize"
        :key="size.value"
        class="!list-none menu-item p-2 rounded"
        :class="{
          'bg-gray-100 font-bold': currentHeading === size.value // 👈 highlight
        }"
        @click="applyFontWeight(size.value, closeDropdown)"
      >
        <span v-html="`<h${size.value} class='!m-1'>${size.label}</h${size.value}>`"></span>
      </li>
    </ul>
  </template>
</BaseDropdown>
          </span>

          <button
            :disabled="!editor.can().chain().focus().toggleItalic().run()"
            :class="{ 'is-active': editor.isActive('italic') }"
            @click="editor.chain().focus().toggleItalic().run()"
          >
            <Italic class="size-4 text-black font-bold"/>
          </button>
          <button
            :disabled="!editor.can().chain().focus().toggleStrike().run()"
            :class="{ 'is-active': editor.isActive('strike') }"
            @click="editor.chain().focus().toggleStrike().run()"
          >
            <Strikethrough class="size-4 text-black"/>
          </button>
          <button
            :disabled="!editor.can().chain().focus().toggleCode().run()"
            :class="{ 'is-active': editor.isActive('code') }"
            @click="editor.chain().focus().toggleCode().run()"
          >
            <Code class="size-4 text-black"/>
          </button>

          <button @click="editor.chain().focus().clearNodes().run()">
            <!-- <img
              src="/public/icons/text-editor/clear-formatting.svg"
              class="size-4"
            /> -->
            <Eraser class="size-4 text-black"/>
          </button>
          <button
            :class="{ 'is-active': editor.isActive('bulletList') }"
            @click="editor.chain().focus().toggleBulletList().run()"
          >
            <List class="size-4 text-black"/>
          </button>
          <button
            :class="{ 'is-active': editor.isActive('orderedList') }"
            @click="editor.chain().focus().toggleOrderedList().run()"
          >
            <ListOrdered class="size-4 text-black"/>
          </button>
          <span>
            <input
              v-show="false"
              ref="uploadImg"
              type="file"
              accept="image/*"
              @change="onImageUpload"
            />
            <button @click="$refs.uploadImg?.click()">
              <Image class="size-4 text-black"/>
            </button>
          </span>
          <button
            :class="{ 'is-active': editor.isActive('blockquote') }"
            @click="editor.chain().focus().toggleBlockquote().run()"
          >
            <Quote class="size-4 text-black"/>
          </button>

          <span class="flex items-center gap-2">
            <BaseDropdown position="bottom-center">
              <template #trigger>
                <TableProperties class="size-4 text-black"/>
              </template>
              <template #content="{ closeDropdown }">
                <BaseCustomGrid
                  @select="
                    (val) => {
                      handleTableAction('insertTable', val);
                      closeDropdown();
                    }
                  "
                />
              </template>
            </BaseDropdown>
            <BaseDropdown position="bottom-center">
              <template #trigger>
                <ChevronDown class="size-4 text-black"/>
              </template>
              <template #content="{ closeDropdown }">
                <ul class="!bg-white max-h-[15rem] !overflow-y-scroll">
                  <li
                    v-for="action in tableActions"
                    :key="action.value"
                    class="!list-none p-2 rounded"
                    :class="{ 'menu-item': !action.title }"
                    @click="[handleTableAction(action.value), closeDropdown()]"
                  >
                    <div
                      v-if="action.title"
                      class="text-light-black font-semibold"
                    >
                      {{ action.title }}
                    </div>
                    <div v-else>{{ action.label }}</div>
                  </li>
                </ul>
              </template>
            </BaseDropdown>
          </span>
          <button @click="editor.chain().focus().setHorizontalRule().run()">
            <Rows2 class="size-4 text-black"/>
          </button>
        </div>
      </div>
      <div class="flex-1 bg-[#FFFFFF] h-[300px] overflow-auto">
        <editor-content
          :editor="editor"
          class="p-2 default-css max-w-full overflow-auto"
          :style="{height: props.height + 'px'}"
        />
        <!-- <div v-html="buttonHtml" /> -->
        <!-- class="p-2 h-[calc(100%-5rem)] default-css max-w-full scroll" -->
      </div>
    </div>
    <!-- <div class="text-danger text-xs">{{ errorMessage }}</div> -->
  </div>
</template>


<style>
@import url("/assets/css/default-style.css");
.button-group {
  padding: 0.4rem;
  border-radius: 0.5rem;
  background: var(--theme-light-gray-color);
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 0.5rem;
}
.button-group button {
  padding: 0.5rem !important;
  background: transparent !important;
  outline: none !important;
  border: none !important;
  cursor: pointer !important;
  border-radius: 0.5rem;
}
.tiptap {
  border: none;
  outline: none;
  height: 100%;
  font-size: 1.2rem;
}
.tiptap:focus-visible {
  outline: none !important;
}

.editor-wrapper .is-active {
  background: #d3d3d3 !important;
}
</style>
