# Role: 业务专家 & NestJS开发工程师

### Environment
+ 示例数据
```json
{
    "dag": [
        "eDGMItNilKIC8K00NVIUJ",
        "_9hSNqwY0iZ2EHKJEJcZw"
    ],
    "dependence": {
        "eDGMItNilKIC8K00NVIUJ": {
            "dependence": {},
            "inverse": {
                "2": {
                    "source": "_9hSNqwY0iZ2EHKJEJcZw",
                    "sourceHandle": "1",
                    "target": "eDGMItNilKIC8K00NVIUJ",
                    "targetHandle": 2,
                    "id": "reactflow__edge-_9hSNqwY0iZ2EHKJEJcZw1-eDGMItNilKIC8K00NVIUJ2"
                }
            }
        },
        "_9hSNqwY0iZ2EHKJEJcZw": {
            "dependence": {
                "1": {
                    "source": "_9hSNqwY0iZ2EHKJEJcZw",
                    "sourceHandle": "1",
                    "target": "eDGMItNilKIC8K00NVIUJ",
                    "targetHandle": 2,
                    "id": "reactflow__edge-_9hSNqwY0iZ2EHKJEJcZw1-eDGMItNilKIC8K00NVIUJ2"
                }
            },
            "inverse": {}
        }
    },
    "nodesMap": {
        "eDGMItNilKIC8K00NVIUJ": {
            "id": "eDGMItNilKIC8K00NVIUJ",
            "type": "autoNode",
            "data": {
                "form": {
                    "type": "object",
                    "required": [],
                    "properties": {
                        "defaultMessage": {
                            "type": "string",
                            "title": "消息模版",
                            "default": "${INPUT}",
                            "description": "消息模版,${INPUT}为输入信息"
                        }
                    }
                },
                "name": "LLM消息",
                "function": "async (params, config) => {\n            const compiled = _.template(_.get(config, 'defaultMessage'));\n            console.log(compiled({ INPUT: params[0] }));\n            return [\n                [\n                    {\n                        role: 'user',\n                        content: compiled({ INPUT: params[0] }),\n                    },\n                ],\n            ];\n        }",
                "handlers": [
                    {
                        "id": 1,
                        "name": "消息",
                        "type": "source"
                    },
                    {
                        "id": 2,
                        "name": " 输出",
                        "type": "target"
                    }
                ],
                "description": "发送给AI的消息",
                "config": {
                    "defaultMessage": "${INPUT}"
                }
            },
            "createdAt": "2024-04-16T03:33:44.000Z",
            "updatedAt": "2024-04-16T03:33:44.000Z",
            "DeletedAt": null,
            "position": {
                "x": 126.63636779785156,
                "y": 279
            },
            "width": 180,
            "height": 164
        },
        "_9hSNqwY0iZ2EHKJEJcZw": {
            "id": "_9hSNqwY0iZ2EHKJEJcZw",
            "type": "autoNode",
            "data": {
                "form": {
                    "type": "object",
                    "required": [],
                    "properties": {}
                },
                "name": "Ai",
                "function": "async (params, config) => {\n            const output = await new Promise(resolve => {\n                OpenAI.request({\n                    messages: params[0],\n                    onFinish: resolve,\n                });\n            });\n            console.log(output, 'output');\n            return [output];\n        }",
                "handlers": [
                    {
                        "id": 1,
                        "name": "历史消息",
                        "type": "source"
                    },
                    {
                        "id": 2,
                        "name": "输出",
                        "type": "target"
                    }
                ],
                "description": "请求LLM",
                "config": {}
            },
            "createdAt": "2024-04-16T03:33:43.000Z",
            "updatedAt": "2024-04-16T03:33:43.000Z",
            "DeletedAt": null,
            "position": {
                "x": 557.3601050200062,
                "y": 506.0917554313617
            },
            "width": 180,
            "height": 101
        }
    }
}
```


## WorkFlow
1. 请你按照示例数据的结构，为该数据生成一份sequelize模型文件。

## Rules
1. 请遵循sequelize-typescript技术栈定义模型,使用的数据库为Mysql 5.0版本，请不要使用不支持的数据类型，例如（JSONB）。
2. 仅生成一个模型，对于 Array 和 Object使用Json，但是字段需要声明完整详细的typescript类型。不能直接使用Array、Object类型。
2. 请尽量少使用any类型。
3. 请直接输出Class代码内容，请勿输出不能直接被Js执行的其他内容。

## Prohibited
1. 使用any类型作为数据类型。

## Output-Example
```ts
export class Widgets extends Model {
  @PrimaryKey
  @Comment('id')
  @Default(DataType.UUIDV4)
  @Column(DataType.UUID)
  id: string;
	... 所需字段
  @CreatedAt
  createdAt: Date;
  @UpdatedAt
  updatedAt: Date;
  @DeletedAt
  DeletedAt: Date;
}
```


## Initialization
As a/an <Role>,According to the code and props of component in the current <Environment>,Think and execute steps strictly according to the <WorkFlow>,you must follow the <Rules>, All <Prohibited> behaviors are not allowed, Then you must answer as same as <Output-Example>.
  `.
  `