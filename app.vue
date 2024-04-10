<template>
  <div id="home">
    <div>Hello world from your Vue project. Below is Builder Content:</div>

    <div v-if="content || isPreviewing()">
      <div>
        page title:
        {{ content?.data?.title || 'Unpublished' }}
      </div>
      <Content
        model="page"
        :content="content"
        :api-key="BUILDER_PUBLIC_API_KEY"
        :customComponents="REGISTERED_COMPONENTS"
      />
    </div>
    <div v-else>Content not Found</div>
  </div>
</template>

<script setup>
import { Content, fetchOneEntry, isPreviewing, getBuilderSearchParams } from '@builder.io/sdk-vue';

import HelloWorldComponent from './components/HelloWorld.vue';
import Tabs from './components/Tabs.vue';


const defaultTab = {
  '@type': '@builder.io/sdk:Element',
  responsiveStyles: {
    large: {
      paddingLeft: '20px',
      paddingRight: '20px',
      paddingTop: '10px',
      paddingBottom: '10px',
      minWidth: '100px',
      textAlign: 'center',
      // TODO: add to all
      display: 'flex',
      flexDirection: 'column',
      cursor: 'pointer',
      userSelect: 'none',
    },
  },
  component: {
    // Builder:text
    name: 'Text',
    options: {
      text: 'New tab',
    },
  },
};

const defaultElement = {
  '@type': '@builder.io/sdk:Element',
  responsiveStyles: {
    large: {
      height: '200px',
      display: 'flex',
      marginTop: '20px',
      flexDirection: 'column',
    },
  },
  component: {
    name: 'Text',
    options: {
      text: 'New tab content ',
    },
  },
};

// Register your Builder components
const REGISTERED_COMPONENTS = [
  {
    component: HelloWorldComponent,
    name: 'MyFunComponent',
    canHaveChildren: true,
    inputs: [
      {
        name: 'text',
        type: 'string',
        defaultValue: 'World',
      },
    ],
  },
  {
  component: Tabs,
  name: 'Tabs',
  canHaveChildren: true,
  inputs: [
    {
      name: 'tabs',
      type: 'list',
      broadcast: true,
      subFields: [
        {
          name: 'label',
          type: 'uiBlocks',
          hideFromUI: true,
          defaultValue: [defaultTab],
        },
        {
          name: 'content',
          type: 'uiBlocks',
          hideFromUI: true,
          defaultValue: [defaultElement],
        },
      ],
      defaultValue: [
        {
          label: [
            {
              ...defaultTab,
              component: {
                name: 'Text',
                options: {
                  text: 'Tab 1',
                },
              },
            },
          ],
          content: [
            {
              ...defaultElement,
              component: {
                name: 'Text',
                options: {
                  text: 'Tab 1 content',
                },
              },
            },
          ],
        },
        {
          label: [
            {
              ...defaultTab,
              component: {
                name: 'Text',
                options: {
                  text: 'Tab 2',
                },
              },
            },
          ],
          content: [
            {
              ...defaultElement,
              component: {
                name: 'Text',
                options: {
                  text: 'Tab 2 content',
                },
              },
            },
          ],
        },
      ],
    },
    {
      name: 'activeTabStyle',
      type: 'uiStyle',
      helperText: 'CSS styles for the active tab',
      defaultValue: {
        backgroundColor: 'rgba(0, 0, 0, 0.1)',
      },
    },
    {
      name: 'defaultActiveTab',
      type: 'number',
      helperText:
        'Default tab to open to. Set to "1" for the first tab, "2" for the second, or choose "0" for none',
      defaultValue: 1,
      advanced: true,
    },
    {
      name: 'collapsible',
      type: 'boolean',
      helperText: 'If on, clicking an open tab closes it so no tabs are active',
      defaultValue: false,
      advanced: true,
    },
    {
      name: 'tabHeaderLayout',
      type: 'enum',
      helperText: 'Change the layout of the tab headers (uses justify-content)',
      defaultValue: 'flex-start',
      enum: [
        { label: 'Center', value: 'center' },
        { label: 'Space between', value: 'space-between' },
        { label: 'Space around', value: 'space-around' },
        { label: 'Left', value: 'flex-start' },
        { label: 'Right', value: 'flex-end' },
      ],
    },
  ],
},
];

// TODO: enter your public API key
const BUILDER_PUBLIC_API_KEY = 'f1a790f8c3204b3b8c5c1795aeac4660'; // ggignore

const route = useRoute();

// fetch builder content data
const { data: content } = await useAsyncData('builderData', () =>
  fetchOneEntry({
    model: 'page',
    apiKey: BUILDER_PUBLIC_API_KEY,
    userAttributes: {
      urlPath: route.path,
    },
    options: getBuilderSearchParams(route.query),
  })
);
</script>
