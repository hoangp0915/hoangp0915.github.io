# custom-input.component.ts
```typescript
import { Component, OnInit, Input } from '@angular/core';
import { Config } from './config.dto';
import { FormGroup, Validators, FormBuilder, FormArray, AbstractControl, ValidatorFn, Validator } from '@angular/forms';
import { Subscription } from 'rxjs';
import { LoadDataService } from '../services/load-data.service';
import { TEMPLATE } from '../helpers/constants.helper';


@Component({
  selector: 'app-custom-input',
  templateUrl: './custom-input.component.html',
  styleUrls: ['./custom-input.component.css']
})
export class CustomInputComponent implements OnInit {
  @Input() init: number;
  @Input() load: string;
  @Input() template: string;
  public dynamicForm: FormGroup;
  private listSub: Subscription[] = [];
  private configs: Config[];
  public isShow = false;
  public isCreate = false;
  public templates = TEMPLATE;

  constructor(private readonly formBuilder: FormBuilder, private loadDataService: LoadDataService) {
    this.createForm();
    this.loadConfig();
  }

  ngOnInit() {
  }

  /**
   * Create form
   *
   * @private
   * @memberof CustomInputComponent
   */
  private createForm() {
    this.dynamicForm = this.formBuilder.group({});
  }
  // private createForm() {
  //   this.dynamicForm = this.formBuilder.group({
  //     key: this.formBuilder.array([])
  //   });
  // }

  /**
   * Create withd
   *
   * @private
   * @returns
   * @memberof CustomInputComponent
   */
  private createItem(validators: any) {
    return this.formBuilder.group({
      value: ['A', this.buildValidation(validators)]
    });
  }

  /**
   * Load config and load data
   *
   * @private
   * @memberof CustomInputComponent
   */
  private loadConfig() {
    this.listSub.push(this.loadDataService.loadConfigItem(this.init).subscribe((cnf: Config[]) => {
      this.configs = cnf;

      //CForm Group -> Object
      this.configs.forEach((config: Config, index) => {
        this.dynamicForm.addControl(`${index}`, this.formBuilder.array([]));
        const key = this.dynamicForm.get(`${index}`) as FormArray;
        for (let i = 0; i < config.repeat; i++) {
          key.push(this.createItem(config.validators));
          // key.get(`${i}.value`).setValue(data[i]);
        }
      });

      //Form Array -> Array
      // this.configs.forEach((config: Config, index) => {
      //   const key = this.dynamicForm.get('key') as FormArray;
      //   const group = this.formBuilder.group({});
      //   group.addControl(`${index}`, this.formBuilder.array([]));
      //   key.push(group);
      //   for (let i = 0; i < config.repeat; i++) {
      //     const widthItem = this.dynamicForm.get(`key.${index}.${index}`) as FormArray;
      //     widthItem.push(this.createItem(config.validators));
      //     // key.get(`${i}.value`).setValue(data[i]);
      //   }
      // });

      // this.listSub.push(this.loadDataService.loadProductData(this.load).subscribe((data: any[]) => {
      //   if (data.length) {
      //     for (let i = 0; i < this.config.repeat; i++) {
      //       width.push(this.createWidth(this.config.validators));
      //       width.get(`${i}.value`).setValue(data[i]);
      //     }
      //   } else {
      //     this.isCreate = true;
      //   }
      // }));
    }));
    console.log('Dynamic Form: ', this.dynamicForm.controls)
  }

  private buildValidation(validators: any) {
    const validList = [];
    Object.keys(validators).forEach(validator => {
      validList.push(this.validator(validator, validators));
    });
    return validList;
  }

  private validator(validator: string, validators: any): ValidatorFn {
    if (validator === 'required') { return Validators.required; }
    if (validator === 'email') { return Validators.email; }
    return Validators[validator](validators[validator].value);
  }

  /**
   * Get Validation
   *
   * @param {*} control
   * @param {boolean} isShow
   * @returns {boolean}
   * @memberof CustomInputComponent
   */
  public getValidation(control, isShow: boolean): boolean {
    return (control.invalid && (control.touched || control.dirty || isShow) || control.errors) ? true : false
  }

  /**
   * On Submut
   *
   * @returns
   * @memberof CustomInputComponent
   */
  onSubmit() {
    this.isShow = true;
    if (this.dynamicForm.invalid) {
      return;
    }
    console.log(this.getRequestBody());
  }

  /**
   * Get Request body
   *
   * @returns
   * @memberof CustomInputComponent
   */
  public getRequestBody() {
    const value = [];
    console.log("CustomInputComponent -> getRequestBody -> this.dynamicForm.value", this.dynamicForm.value)
  }

}

```
# custom-input.component.html
```html
<!-- <div class="container px-2">
  <form [formGroup]="dynamicForm" class="mt-5">
    <div class="form-row">
      <div class="col-12">
        <div class="row">
          <div class="col-4">
            Product Id
          </div>
          <div class="col-8">
            <input class="form-control" formControlName="productId" type="text">
            <input class="form-control" formControlName="productId" type="text">
          </div>
        </div>
        <div class="row">
          <div class="col-4">
            Width
          </div>
          <div class="col-8" formArrayName="width">
            <div *ngFor="let item of dynamicForm.get('width').controls; let i = index" [formGroupName]="i">
              <input class="form-control" [type]="config.dataType" formControlName="value"
                [ngClass]="{'is-invalid': getValidation(dynamicForm.get('width.' + i + '.value'), isShow)}">
              <div *ngIf="getValidation(dynamicForm.get('width.' + i + '.value'), isShow)" class="invalid-feedback">
                <div *ngFor="let item of config.validators | keyvalue">
                  <div
                    *ngIf="dynamicForm.get('width.' + i + '.value').errors && dynamicForm.get('width.' + i + '.value').errors[item.key.toLowerCase()]">
                    {{ item.value.message }}
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <pre>Valid: {{dynamicForm.valid}}</pre>
        <div class="row">
          <div class="col-12">
            <button type="button" class="btn btn-primary mr-3" (click)="onSubmit()">Submit</button>
            <button type="button" class="btn btn-primary" (click)="addControl()">Add</button>
          </div>
        </div>
      </div>
    </div>
  </form>
</div> -->
<form [formGroup]="dynamicForm">
  <div class="row" *ngIf="template == templates.one_column">
    <div class="col-6">
      <div class="form-group" *ngFor="let item of configs; let idexGroup = index" [formGroupName]="idexGroup">
        <label>{{item.item}}</label>
        <div *ngFor="let item of dynamicForm.get(idexGroup +'').controls; let i = index" [formGroupName]="i">
          <input [type]="item.dataType" formControlName="value" class="form-control mb-1" id="formGroupExampleInput"
            placeholder="Example input">
        </div>
      </div>
    </div>
    <!-- <div class="col-6" formArrayName="key">
      <div class="form-group" *ngFor="let item of configs; let idexGroup = index" [formGroupName]="idexGroup">
        <label for="formGroupExampleInput">{{item.item}}</label>
        <div [formArrayName]="idexGroup">
          <div *ngFor="let item of dynamicForm.get('key.'+ idexGroup +'.'+ idexGroup).controls; let i = index"
            [formGroupName]="i">
            <input [type]="item.dataType" formControlName="value" class="form-control mb-1" id="formGroupExampleInput"
              placeholder="Example input">
          </div>
        </div>
      </div>
    </div> -->

  </div>
  <div class="row" *ngIf="template == templates.two_column">
    <div class="col-6" *ngFor="let item of configs; let idexGroup = index" [formGroupName]="idexGroup">
      <div class="form-group">
        <label for="formGroupExampleInput">{{item.item}}</label>
        <div *ngFor="let item of dynamicForm.get(idexGroup +'').controls; let i = index" [formGroupName]="i">
          <input [type]="item.dataType" formControlName="value" class="form-control mb-1" id="formGroupExampleInput"
            placeholder="Example input">
        </div>
      </div>
    </div>
    <!-- <div class="col-6">
      <div class="form-group">
        <label for="formGroupExampleInput">B</label>
        <input type="text" class="form-control mb-1" id="formGroupExampleInput" placeholder="Example input">
        <input type="text" class="form-control mb-1" id="formGroupExampleInput" placeholder="Example input">
        <input type="text" class="form-control mb-1" id="formGroupExampleInput" placeholder="Example input">
      </div>
    </div> -->

  </div>
</form>
<pre>{{dynamicForm.value | json}}</pre>
<pre>{{dynamicForm.errors | json}}</pre>
<button (click)="onSubmit()">Submit</button>

```

# config.json
```json
[{
    "item": "Product id",
    "dataType": "text",
    "precision": 3,
    "repeat": 1,
    "validators": {
      "required": {
        "value": true,
        "message": "Value is required"
      }
    }
  },
  {
    "item": "Width",
    "dataType": "number",
    "precision": 3,
    "repeat": 3,
    "validators": {
      "required": {
        "value": true,
        "message": "Value is required"
      },
      "maxLength": {
        "value": 5,
        "message": "Value must be less than or equal to 100 characters"
      },
      "minLength": {
        "value": 1,
        "message": "Value must be at least 1 characters"
      },
      "max": {
        "value": 100,
        "message": "Value must be smaller or equal 100"
      },
      "min": {
        "value": 1,
        "message": "Value must be greater 1"
      }
    }
  },
  {
    "item": "Product id",
    "dataType": "text",
    "precision": 3,
    "repeat": 1,
    "validators": {
      "required": {
        "value": true,
        "message": "Value is required"
      }
    }
  },
  {
    "item": "Product id",
    "dataType": "text",
    "precision": 3,
    "repeat": 1,
    "validators": {
      "required": {
        "value": true,
        "message": "Value is required"
      }
    }
  }
]

```
