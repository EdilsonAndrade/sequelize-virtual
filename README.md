# Sequelize Virtual field

Campos virtuals são necessários para cando queremos devolver uma informação para o cliente e que não haja necessidade de salvar a mesma no banco.

Como por exemplo, quero mostrar se determinado registro de um agendamento é possível o cancelament ou não, ou se a data do agendamento é passada, confome o exemplo abaixo com a utilização do Sequelize.VIRTUAL 
### No entanto deve-se entender que na controller que retorna os campos, deve informar também o campo virtual, caso contrário não é demonstrado no response da requisição.

```javascript
past: {
    type: Sequelize.VIRTUAL,
    get() {
      return isBefore(this.date, new Date());
    },
  },
  cancelable: {
    type: Sequelize.VIRTUAL,
    get() {
      return isBefore(new Date(), subHours(this.date, 2));
    },
  },
  ```
        
Código completo de onde já fiz utilização deste virtual.

```javascript
import { isBefore, subHours } from 'date-fns';
import { Model, Sequelize } from 'sequelize';

class Appointment extends Model {
  static init(connection) {
    super.init(
      {
        date: Sequelize.DATE,
        canceled_at: Sequelize.DATE,
        past: {
          type: Sequelize.VIRTUAL,
          get() {
            return isBefore(this.date, new Date());
          },
        },
        cancelable: {
          type: Sequelize.VIRTUAL,
          get() {
            return isBefore(new Date(), subHours(this.date, 2));
          },
        },
      },
      {
        sequelize: connection,
      }
    );
    return this;
  }

  static associate(models) {
    this.belongsTo(models.User, { foreignKey: 'user_id', as: 'user' });
    this.belongsTo(models.User, { foreignKey: 'provider_id', as: 'provider' });
  }
}

export default Appointment;

```
