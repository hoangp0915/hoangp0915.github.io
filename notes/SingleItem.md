# AppComponent

## HTML

```html
<div class="container">
  <h1>Dynamic Form</h1>
  <hr />
  <app-single-item></app-single-item>
  <button class="btn btn-primary" (click)="onSubmit()">Submit</button>
</div>
```

## TS

```typescript
@ViewChild(SingleItemComponent) single;
  public template = TEMPLATE.one_column;

  constructor() { }
  ngOnInit(): void {
    const params = {
      init: 1,
      load: 'NHAC-1234'
    }
    this.single.initConfig(params);
  }

  /**
   * On Submit
   *
   * @memberof AppComponent
   */
  public onSubmit() {
    this.single.save();
  }
```

# ConfigDto

```typescript
export class ConfigDto {
  public item: string;
  public dataType: string;
  public repeat: number;
  public precision?: number;
  public validators: ValidatorsDto;
  public values?: any[];
  public properties?: PropertyDto[];
  public showHint: boolean;
  public valueTable: string;
  public valueField: string;
}

export interface ValidatorsDto {
  required?: boolean;
  pattern?: string;
  min?: number;
  max?: number;
}

export interface PropertyDto {
  item: string;
  property: string;
  qrcode: boolean;
  validators?: ValidatorsDto;
  value?: number;
  visible: boolean;
}
```

# TooltipDirective

```typescript
import { Directive, ElementRef, Input, OnInit } from "@angular/core";
declare var $: any;

@Directive({
  selector: "[appTooltip]",
})
export class TooltipDirective implements OnInit {
  @Input("appTooltip") showHint: string;

  constructor(private er: ElementRef) {}

  ngOnInit(): void {
    $(this.er.nativeElement).tooltip(this.showHint ? "enable" : "disable");
  }
}
```

# ReplaceParamPipe

```typescript
GenerateInput github
```

# ManufacturingServiceService

```typescript
import { Injectable } from "@angular/core";
import { HttpClient, HttpParams } from "@angular/common/http";
import { Observable } from "rxjs";
import { environment } from "../../environments/environment";
import { URL_CONSTANTS } from "../helpers/constants.helper";

@Injectable({
  providedIn: "root",
})
export class ManufacturingServiceService {
  constructor(private http: HttpClient) {}

  /**
   * Load config from serve
   *
   * @param {*} params
   * @returns {Observable<Object>}
   * @memberof ManufacturingServiceService
   */
  public loadConfigItem(params: any): Observable<Object> {
    const url = `${URL_CONSTANTS.BASE_URL}/${environment.contextPath}/${URL_CONSTANTS.CONFIGS}`;
    // const url = 'assets/config.json'
    const param = this.convertHttpParams(params);
    return this.http.get(url, { params: param });
  }

  /**
   *
   *
   * @param {*} body
   * @returns {Observable<Object>}
   * @memberof ManufacturingServiceService
   */
  public save(body: any): Observable<Object> {
    const url = `${URL_CONSTANTS.BASE_URL}/${environment.contextPath}/${URL_CONSTANTS.CONFIGS}`;
    return this.http.put(url, body);
  }

  /**
   * Convert params
   *
   * @private
   * @param {*} params
   * @returns {HttpParams}
   * @memberof ManufacturingServiceService
   */
  private convertHttpParams(params): HttpParams {
    let httpParams = new HttpParams();
    if (params) {
      for (const key in params) {
        const value = params[key].toString();
        if (params.hasOwnProperty(key) && value) {
          httpParams = httpParams.append(key, value);
        }
      }
    }
    return httpParams;
  }
}
```

# ValidationMessageComponent

# SingleItemComponent

## HTML

```html
<form [formGroup]="dynamicForm">
  <div class="row">
    <div class="col-12">
      <div
        class="col-12"
        *ngIf="
          config !== undefined && config.properties && config.properties.length
        "
        formGroupName="properties"
      >
        <div
          class="form-group mb-2px"
          *ngFor="let item of config.properties; let i = index"
        >
          <div class="row" *ngIf="item.visible">
            <div class="col-2">
              <label>{{ item.item }}: </label>
            </div>
            <div class="col-10">
              <div class="d-flex bd-highlight">
                <div class="flex-grow-1 bd-highlight">
                  <input
                    [appTooltip]="config.showHint"
                    [attr.data-original-title]="
                      dynamicForm.get('properties.' + item.property).value
                    "
                    data-toggle="tooltip"
                    data-placement="right"
                    [formControlName]="item.property"
                    class="form-control"
                    [ngClass]="
                      validation.class(
                        dynamicForm.get('properties.' + item.property),
                        validation.isShow
                      )
                    "
                  />
                  <app-validation-message
                    [messages]="validation.messages"
                    [control]="dynamicForm.get('properties.' + item.property)"
                    [isShow]="validation.isShow"
                    [validators]="item.validators"
                  ></app-validation-message>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div
        class="col-12"
        *ngIf="config !== undefined && config.repeat"
        formArrayName="width"
      >
        <div class="form-group">
          <div class="row">
            <div class="col-2">
              <label>{{ config.item }}: </label>
            </div>
            <div class="col-10">
              <div
                *ngFor="
                  let item of dynamicForm.get('width').controls;
                  let i = index
                "
                [formGroupName]="i"
                class="d-flex bd-highlight mb-2px"
              >
                <div class="flex-grow-1 bd-highlight mr-2px">
                  <input
                    [appTooltip]="config.showHint"
                    [attr.data-original-title]="
                      dynamicForm.get('width.' + i + '.value').value
                    "
                    data-toggle="tooltip"
                    [type]="config.dataType"
                    formControlName="value"
                    class="form-control"
                    [ngClass]="
                      validation.class(
                        dynamicForm.get('width.' + i + '.value'),
                        validation.isShow
                      )
                    "
                  />
                  <app-validation-message
                    [messages]="validation.messages"
                    [control]="dynamicForm.get('width.' + i + '.value')"
                    [isShow]="validation.isShow"
                    [validators]="_validators"
                    [precision]="config.precision"
                  ></app-validation-message>
                </div>
                <div class="bd-highlight" *ngIf="config.qrcode">
                  <button type="submit" class="btn btn-primary">
                    <i class="fas fa-qrcode"></i>
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</form>
```

## TS

```typescript
import {
  Component,
  OnInit,
  OnChanges,
  OnDestroy,
  Input,
  SimpleChanges,
} from "@angular/core";
import { Subscription } from "rxjs";
import { TEMPLATE, VALIDATORS, DATA_TYPE } from "../helpers/constants.helper";
import { ConfigDto, PropertyDto } from "../core/models/config.dto";
import {
  FormGroup,
  FormBuilder,
  ValidatorFn,
  Validators,
  FormArray,
} from "@angular/forms";
import { validationClass } from "../core/common/validation-message/validation-message.component";
import { ManufacturingServiceService } from "../services/manufacturing-service.service";
import { finalize } from "rxjs/operators";
import messagesJson from "../../assets/message.json";

@Component({
  selector: "app-single-item",
  templateUrl: "./single-item.component.html",
  styleUrls: ["./single-item.component.css"],
})
export class SingleItemComponent implements OnInit, OnChanges, OnDestroy {
  @Input() template: string;
  @Input() messages;
  private listSub: Subscription[] = [];
  public _template = TEMPLATE;
  public configs: ConfigDto[];
  public config: ConfigDto;
  public dynamicForm: FormGroup;
  public _validators;
  public validation = {
    messages: messagesJson,
    isShow: false,
    class: validationClass,
  };
  public params: {};
  constructor(
    private formBuilder: FormBuilder,
    private manufacturingServiceService: ManufacturingServiceService
  ) {
    this.createForm();
  }

  ngOnChanges(changes: SimpleChanges): void {
    if (changes.messages) {
      this.validation.messages = changes.messages.currentValue;
    }
  }

  ngOnInit() {}

  /**
   * Init config
   *
   * @param {*} params
   * @memberof SingleItemComponent
   */
  public initConfig(params): void {
    this.params = params;
    this.listSub.push(
      this.manufacturingServiceService
        .loadConfigItem(params)
        .pipe(
          finalize(() => {
            this.loadConfig();
          })
        )
        .subscribe(
          (config: ConfigDto) => {
            this.config = config;
          },
          (error) => {
            console.warn(
              "MultipleInputComponent -> initConfig -> error",
              error
            );
          }
        )
    );
  }

  /**
   * Create form
   *
   * @memberof SingleItemComponent
   */
  public createForm() {
    this.dynamicForm = this.formBuilder.group({});
  }

  /**
   * Create item
   *
   * @private
   * @param {*} validators
   * @param {number} [precision]
   * @returns
   * @memberof SingleItemComponent
   */
  private createItem(validators: any, precision?: number, data_type?: string) {
    return this.formBuilder.group({
      value: ["", this.buildValidation(validators, precision, data_type)],
    });
  }

  /**
   * Load config
   *
   * @private
   * @memberof SingleItemComponent
   */
  private loadConfig() {
    //clear form group
    if (Object.keys(this.dynamicForm.controls).length) {
      Object.keys(this.dynamicForm.controls).forEach((element) => {
        this.dynamicForm.removeControl(element);
      });
    }
    if (this.config) {
      if (this.config.repeat) {
        this.dynamicForm.addControl("width", this.formBuilder.array([]));
        this.loadWidth(this.dynamicForm.get("width") as FormArray);
      }
      if (this.config.properties && this.config.properties.length) {
        this.dynamicForm.addControl(`properties`, this.formBuilder.group({}));
        this.loadProperties(this.dynamicForm.get("properties") as FormGroup);
      }
    }
  }

  /**
   * Load width
   *
   * @private
   * @param {FormArray} width
   * @memberof SingleItemComponent
   */
  private loadWidth(width: FormArray) {
    this._validators = this.convertValidator(
      this.config.validators,
      this.config.dataType
    );
    while (width.length) {
      width.removeAt(width.length - 1);
    }
    if (this.config.repeat) {
      for (let i = 0; i < this.config.repeat; i++) {
        // push group into formArray
        width.push(
          this.createItem(
            this._validators,
            this.config.precision,
            this.config.dataType
          )
        );
        width.get(`${i}.value`).setValue(this.config.values[i]);
      }
    }
  }

  /**
   * Load properties
   *
   * @private
   * @param {FormGroup} properties
   * @memberof SingleItemComponent
   */
  private loadProperties(properties: FormGroup) {
    if (Object.keys(properties.controls).length) {
      Object.keys(properties.controls).forEach((element) => {
        properties.removeControl(element);
      });
    }
    this.config.properties.forEach((property: PropertyDto) => {
      properties.addControl(
        property.property,
        this.formBuilder.control(
          property.value,
          this.buildValidation(property.validators)
        )
      );
    });
  }

  /**
   * Convert validation
   *
   * @private
   * @param {*} validators
   * @param {string} [data_type]
   * @returns
   * @memberof SingleItemComponent
   */
  private convertValidator(validators: any, data_type?: string) {
    const validList = {};
    Object.keys(validators).forEach((validator) => {
      if (validator === VALIDATORS.min || validator === VALIDATORS.max) {
        const key =
          data_type == DATA_TYPE.text ? validator + "Length" : validator;
        validList[key] = validators[validator];
      } else {
        validList[validator] = validators[validator];
      }
    });
    return validList;
  }

  /**
   * Build validation
   *
   * @private
   * @param {*} validators
   * @param {number} [precision]
   * @returns {ValidatorFn[]}
   * @memberof SingleItemComponent
   */
  private buildValidation(
    validators: any,
    precision?: number,
    data_type?: string
  ): ValidatorFn[] {
    const validList = [];
    //validation percision
    if (!!data_type && data_type === DATA_TYPE.number) {
      const precisionValidation = (control: FormGroup) => {
        // const precision = this.config.precision
        if (precision !== undefined) {
          const pattern = new RegExp(`^\\d*(\\.\\d{0,${precision}})?$`, "g");
          if (!!control.value && !pattern.test(control.value)) {
            return { precision: true };
          }
        } else {
          return null;
        }
      };
      validList.push(precisionValidation);
    }
    if (validators !== null) {
      Object.keys(validators).forEach((validator) => {
        if (this.getValidation(validator, validators) !== null) {
          validList.push(this.getValidation(validator, validators));
        }
      });
    }
    return validList;
  }

  /**
   * Get validation
   *
   * @private
   * @param {string} validator
   * @param {*} validators
   * @returns {ValidatorFn}
   * @memberof SingleItemComponent
   */
  private getValidation(validator: string, validators: any): ValidatorFn {
    switch (validator) {
      case VALIDATORS.required:
        return validators.validator ? Validators.required : null;
      case VALIDATORS.email:
        return validators.validator ? Validators.email : null;
      default:
        return Validators[validator](validators[validator]);
    }
  }

  /**
   * Create request body send to server
   *
   * @private
   * @returns
   * @memberof SingleItemComponent
   */
  private createRequestBody() {
    const body = {};
    body["valueTable"] = this.config.valueTable;
    body["valueField"] = this.config.valueField;
    body["load"] = this.params["load"];
    const properties = [];
    if (this.dynamicForm.get("properties")) {
      Object.keys(this.dynamicForm.get("properties").value).forEach((name) => {
        const property = {};
        property["name"] = name;
        property["value"] = this.dynamicForm
          .get("properties")
          .value[name].toString();
        properties.push(property);
      });
    }
    body["properties"] = this.dynamicForm.get("properties") ? properties : null;
    const values = [];
    this.dynamicForm.get("width").value.forEach((element) => {
      values.push(element.value);
    });
    body["values"] = values;
    return body;
  }

  /**
   * Save data
   *
   * @memberof SingleItemComponent
   */
  public save() {
    this.validation.isShow = true;
    if (this.dynamicForm.invalid) return;
    const body = this.createRequestBody();
    this.listSub.push(
      this.manufacturingServiceService.save(body).subscribe((res: any) => {
        this.initConfig(this.params);
      })
    );
  }

  ngOnDestroy(): void {
    this.listSub.forEach((sub) => sub.unsubscribe());
  }
}
```

# package

```json
bootstrap": "^4.5.0",
    "popper.js": "1.14.3",
    "core-js": "^2.5.4",
    "jquery": "^3.5.1",
```

# angular.json

````json
"styles": [
              "node_modules/bootstrap/dist/css/bootstrap.css",
              "node_modules/@fortawesome/fontawesome-free/css/all.css",
              "src/styles.css"
            ],
            "scripts": [
              "node_modules/jquery/dist/jquery.min.js",
              "node_modules/popper.js/dist/umd/popper.js",
              "node_modules/bootstrap/dist/js/bootstrap.min.js"
            ]
            ```
````
