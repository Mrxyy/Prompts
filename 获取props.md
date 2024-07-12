# Role: React/Javascript高级工程师

## Environment
+ 当前组件代码
```js
import { Card, Link } from '@arco-design/web-react';

const App = () => {
  return (
    <div className="h-full">
      <Card
        className="h-full"
        title='自定义组件'
      >
        欢迎使用紫微低代码平台👏
      </Card>
    </div>
  );
};

export default App;
```

+ 解析props的函数generatorConfigForCustom、getControlType代码
```js
import { ValidationTypes } from 'constants/WidgetValidation';
import { EvaluationSubstitutionType } from 'entities/DataTree/dataTreeFactory';

export default function generatorConfigForCustom(
  props: { defaultValue; label; name; Type }[],
  isConsole?
) {
  let Ts = ``;
  const allDefaultValue = {};

  function getControlType({ defaultValue, label, name, Type }) {
    const type = Type.toLowerCase();
    allDefaultValue[name] =
      typeof defaultValue === 'string'
        ? defaultValue.replace(/^(\"|\')|(\"|\')$/g, '')
        : defaultValue;
    if (
      allDefaultValue[name] === '-' ||
      allDefaultValue[name] === 'undefined'
    ) {
      allDefaultValue[name] = undefined;
    }
    if (typeof allDefaultValue[name] === 'string' && allDefaultValue[name]) {
      //处理默认值
      try {
        allDefaultValue[name] = eval(allDefaultValue[name]);
      } catch (e) {}
    }
    Ts += name + ':';
    //文字样式,通过名字匹配
    if (/fontStyle$/i.test(name)) {
      Ts += '"BOLD" | "ITALIC";';
      return {
        propertyName: name,
        label: label,
        helpText: label,
        controlType: 'BUTTON_TABS',
        options: [
          {
            icon: 'BOLD_FONT',
            value: 'BOLD',
            title: '字体加粗',
          },
          {
            icon: 'ITALICS_FONT',
            value: 'ITALIC',
            title: '字体倾斜',
          },
        ],
        isJSConvertible: true,
        isBindProperty: true,
        isTriggerProperty: false,
        validation: {
          type: 'ValidationTypes.TEXT',
        },
      };
    }
    //文字类型，通过名字匹配
    if (/fontFamily$/i.test(name)) {
      defaultValue = 'string';
    }
    //文字对其方式，通过名字匹配
    if (/textAlign$/i.test(name)) {
      Ts += '"LEFT" | "CENTER" | "RIGHT";';
      return {
        propertyName: name,
        label: label,
        helpText: label,
        controlType: 'BUTTON_TABS',
        options: [
          {
            icon: 'LEFT_ALIGN',
            value: 'LEFT',
            title: '左对齐',
          },
          {
            icon: 'CENTER_ALIGN',
            value: 'CENTER',
            title: '居中',
          },
          {
            icon: 'RIGHT_ALIGN',
            value: 'RIGHT',
            title: '右对齐',
          },
        ],
        defaultValue: 'LEFT',
        isJSConvertible: true,
        isBindProperty: true,
        isTriggerProperty: false,
        validation: {
          type: ValidationTypes.TEXT,
        },
      };
    }
    //文字大小，通过名字匹配
    if (/fontSize$/i.test(name)) {
      Ts += '"HEADING1" | "HEADING2" | "HEADING3" | "HEADING4" | "HEADING5";';
      return {
        propertyName: name,
        label: label,
        helpText: label,
        controlType: 'DROP_DOWN',
        options: [
          {
            label: '1级标题',
            value: 'HEADING1',
            subText: '24px',
            icon: 'HEADING_ONE',
          },
          {
            label: '2级标题',
            value: 'HEADING2',
            subText: '18px',
            icon: 'HEADING_TWO',
          },
          {
            label: '3级标题',
            value: 'HEADING3',
            subText: '16px',
            icon: 'HEADING_THREE',
          },
          {
            label: '段落',
            value: 'PARAGRAPH',
            subText: '14px',
            icon: 'PARAGRAPH',
          },
          {
            label: '2级段落',
            value: 'PARAGRAPH2',
            subText: '12px',
            icon: 'PARAGRAPH_TWO',
          },
        ],
        isJSConvertible: true,
        isBindProperty: true,
        isTriggerProperty: false,
        validation: {
          type: ValidationTypes.TEXT,
          params: {
            allowedValues: [
              'HEADING1',
              'HEADING2',
              'HEADING3',
              'PARAGRAPH',
              'PARAGRAPH2',
            ],
          },
        },
      };
    }
    if (type == 'string') {
      Ts += 'string;';
      if (/color$/i.test(name) || /BGC$/i.test(name)) {
        return {
          propertyName: name,
          label: label,
          helpText: label,
          defaultValue: defaultValue,
          controlType: 'COLOR_PICKER',
          isJSConvertible: true,
          isBindProperty: true,
          isTriggerProperty: false,
          validation: {
            type: ValidationTypes.TEXT,
            params: {
              regex: /^(?![<|{{]).+/,
            },
          },
        };
      }
      return {
        propertyName: name,
        label: label,
        helpText: label,
        defaultValue: defaultValue,
        controlType: 'INPUT_TEXT',
        placeholderText: label,
        isBindProperty: true,
        isJSConvertible: true,
        isTriggerProperty: false,
        validation: {
          type: ValidationTypes.TEXT,
        },
      };
    }
    if (type == 'number') {
      Ts += 'number;';
      return {
        propertyName: name,
        label: label,
        helpText: label,
        controlType: 'INPUT_TEXT',
        placeholderText: label,
        isBindProperty: true,
        isJSConvertible: true,
        isTriggerProperty: false,
        validation: {
          type: ValidationTypes.NUMBER,
        },
      };
    }
    if (type == 'boolean') {
      Ts += 'boolean;';
      return {
        propertyName: name,
        label: label,
        helpText: label,
        controlType: 'SWITCH',
        placeholderText: label,
        isBindProperty: true,
        isJSConvertible: true,
        isTriggerProperty: false,
        validation: {
          type: ValidationTypes.BOOLEAN,
        },
      };
    }
    //枚举下拉类型
    if (type.search(/\s?\|\s?/) > 0) {
      const options = type.split(/\s?\|\s?/);
      Ts += 'string[];';
      return {
        propertyName: name,
        label: label,
        helpText: label,
        controlType: 'DROP_DOWN',
        options: options.map((v) => ({
          label: v.replace(/(^("|'))|("|'$)/g, ''),
          value: v.replace(/(^'|")|("|'$)/g, ''),
        })),
        defaultValue: options[0].replace(/(^'|")|("|'$)/g, ''),
        isBindProperty: false,
        isJSConvertible: true,
        isTriggerProperty: false,
      };
    }
    //函数事件类型
    if (/^\(/.test(type)) {
      Ts += '()=>any';
      return {
        propertyName: name,
        label: label,
        helpText: label,
        controlType: 'ACTION_SELECTOR',
        isBindProperty: true,
        isJSConvertible: true,
        isTriggerProperty: true,
        dependencies: [],
      };
    }
    //数组类型
    if (type.search('array') == 0 || type.search('[]') != 0) {
      Ts += 'Record<string,any>[];';
      return {
        propertyName: name,
        label: label,
        helpText: label,
        controlType: 'INPUT_TEXT',
        placeholderText: defaultValue,
        inputType: 'ARRAY',
        isBindProperty: true,
        isJSConvertible: true,
        isTriggerProperty: false,
        validation: {
          type: ValidationTypes.ARRAY,
          params: {
            default: [],
          },
        },
        evaluationSubstitutionType: EvaluationSubstitutionType.SMART_SUBSTITUTE,
      };
    }
    //object类型
    if (type.search('object') == 0) {
      Ts += 'Record<string,any>;';
      return {
        propertyName: name,
        label: label,
        helpText: label,
        controlType: defaultValue,
        inputType: 'OBJECT',
        isBindProperty: true,
        isJSConvertible: true,
        isTriggerProperty: false,
        validation: {
          type: ValidationTypes.OBJECT,
          params: {
            default: [],
          },
        },
        evaluationSubstitutionType: EvaluationSubstitutionType.SMART_SUBSTITUTE,
      };
    }
    return {
      propertyName: name,
      label: label,
      helpText: label,
      controlType: 'INPUT_TEXT',
      placeholderText: label,
      isBindProperty: true,
      isJSConvertible: true,
      isTriggerProperty: true,
      validation: {
        type: ValidationTypes.TEXT,
      },
    };
  }

  Ts += ``;

  const result = {
    paneConfig: props.map(getControlType),
    ts: Ts,
    default: allDefaultValue,
    pickProps: JSON.stringify(Object.keys(allDefaultValue)),
  };
  if (isConsole) {
    console.log('ts配置', Ts);
    console.log('默认值props', allDefaultValue);
    console.log('pick-props', Object.keys(allDefaultValue));
    console.log('面板配置', result.paneConfig);
  }
  return result;
}
```

+ user requirements
可以自定义内容 和 标题

## WorkFlow
1. To enhance the flexibility and reusability of the component, Please analyze the current component code in conjunction with user requirements, abstracting reusable and generic code. Convert these parts into configurable properties (props), allowing customization of the component's behavior through external parameters. Refactor the component to ensure it can adapt to various use cases while maintaining code clarity and maintainability. Upon completion, provide the updated component code.
2. Generate a configuration JSON for props of the new component code that conforms to the props specified in generatorConfigForCustom's first parameter.
3. Check if there is alignment between the props in the component code from step one and the configuration JSON of props from step two. If they are not aligned, restart from step one following <WorkFlow>.
4. Analyze the code to assign a name to the component based on its functionality,英文名称请使用大驼峰命名法,不要有任何空格.

## Rules
1. The type of props generated must be supported by the function getControlType.
2. The label is the Chinese description of the role of prop,
3. The defaultValue of prop can be the value in the original code, a valid value matching the Type of prop, or even null.
4. Please output directly as JSON without any additional content.
5. The value in <Output-Example> is an explanation of the key.
6. props.children 已经被使用，请不要使用 children 作为prop的名称（非常重要!）.

## Prohibited
1. prop's name is one of the values in the array ["children"], 例如: "name": "children".

## Output-Example
{
	"names": {
		"zh": "组件的中文名称",
		"en": "组件英文名称"
	},
	"props": [
		{
			"defaultValue": "prop默认值",
			"label": "prop作用描述",
			"name": "prop名称",
			"Type": "prop数据类型"
		}
		...
	]
}


## Initialization
As a/an <Role>,According to the code in the current <Environment> ,Think and execute steps strictly according to the <WorkFlow>,you must follow the <Rules>, All <Prohibited> behaviors are not allowed, Then you must answer as same as <Output-Example> .
  `