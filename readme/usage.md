### Plugins

These optional plugin properties may be called by `jaid-core`:

Name|Parameters|Return value
---|---|---
`constructor`|`JaidCore core`
`setCoreReference`|`JaidCore core`
`getConfigSetup`||`Object additionalConfigSetup`
`preInit`||`boolean shouldRemovePlugin`
`handleConfig`|`Object config`|`boolean shouldRemovePlugin`
`handleKoa`|`Koa koa`
`handleGot`|`Got got`
`collectModels`||`Object<string, {default: Model, schema: Object}>`
`init`||`boolean shouldRemovePlugin`
`postInit`||`boolean shouldRemovePlugin`
`ready`|
`handleLog`|`string level`, `string[] fragments`|

### JaidCorePlugin

Plugins can inherit from any superclass. When they inherit from JaidCorePlugin, some fields for the instance are automatically set:

- `.core`
- `.logger` (has `.info`, `.warn`, `.error`, `.debug`)
- `.config` (Object of the loaded config)

Plugin example:

```js
import {JaidCorePlugin} from "jaid-core"

export default class Plugin extends JaidCorePlugin {

  constructor(options = {}) {
    super()
    this.options = {
      ...options
    }
  }

  ready() {
    this.log("Hello!")
  }

}
```

Sequelize model example:

```js
import Sequelize from "sequelize"

class PluginModel extends Sequelize.Model {

  /**
   * @return {string}
   */
  getTitle() {
    return this.title
  }

}

/**
 * @type {import("sequelize").ModelAttributes}
 */
export const schema = {
  title: {
    type: Sequelize.STRING,
    allowNull: false
  }
}

export default PluginModel
```

Advanced Sequelize model (dynamically generated):

```js
import Sequelize from "sequelize"

/**
 * @param {typeof import("sequelize").Model} Model
 * @param {import("jaid-core").ModelDefinitionContext} context
 * @return {{default, schema}}
 */
export default (Model, {models}) => {

    class AdvancedModel extends Model {

      /**
       * @param {Object<string, import("sequelize").Model>} models
       */
      static associate() {
        AdvancedModel.belongsTo(models.AnotherModel, {
          foreignKey: {
            allowNull: false,
          },
        })
      }

      /**
       * @return {string}
       */
      getTitle() {
        return this.title
      }

  }

  /**
   * @type {import("sequelize").ModelAttributes}
   */
  const schema = {
    title: {
      type: Sequelize.STRING,
      allowNull: false
    }
  }

  return {
    default: AdvancedModel,
    schema,
  }

}
```