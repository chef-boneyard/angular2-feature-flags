# angular2-feature-flags
Feature flags for Angular2 applications

## Installation and Usage

### ES6 via NPM

    npm install angular2-feature-flags --save

#### Setting up your Feature Flag Dashboard

The Feature Flag Dashboard is what allows you to enable or disable Feature Flags from within the browser.

To get started, import the `FeatureFlagsDashboard` component. We recommend including it in your top-level app component so that the dashboard is available on all your pages. Then, in your component class, define your Feature Flags and the Konami Code you wish to use to toggle the Feature Flags Dashboard. Finally, put the `<feature-flags-dashboard>` directive at the top of your component template.

```javascript
import { FeatureDefinition, FeatureFlagsDashboard } from 'angular2-feature-flags';

@Component({
  selector: 'app',
  directives: [ FeatureFlagsDashboard ],
  template: `
  	<feature-flags-dashboard [features]="features" [code]="konami_code"></feature-flags-dashboard>
    <div>
    	The rest of your content goes here.
    </div>
  `
})
export class App {
  features: Array<FeatureDefinition> = [
  	{
  		'name': 'new-feature',
  		'display': 'New Feature',
  		'description': 'This is a new feature which has yet to be fully release.'
  	},
  	{
  		'name': 'new-code-path',
  		'display': 'New Code Path',
  		'description': "This is a new code path we want to test out."
  	}
  ];
  konami_code: Array<number> = [38, 38, 40, 40, 37, 39, 37, 39, 66, 65, 13];
}
```

For maximum compatibility with old and new browsers, you need to specify your konami code using the [keycode value](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/keyCode). To determine the keycodes for your particular konami code, you can use [this helpful site](http://keycode.info/).

At this point, you should be able to open your application, enter your konami code, and view your Feature Flag Dashboard.

#### Leveraging a Feature Flag in a component using the `<feature-flag>` directive

If all you want to do is simply show or hide some elements when a Feature is enabled or disabled, you can do so by wrapping those elements in the `<feature-flag>` directive. Simply include the `FeatureFlag` component as one of the directives and nest the elements you wish to hide inside it.

```javascript
import { FeatureFlag } from 'angular2-feature-flags';

@Component({
    selector: 'app',
    directives: [ FeatureFlag ],
    template: `
    	<feature-flag [name]='new-feature'>
    		<super-secret-feature></super-secret-feature>
    	</feature-flag>
    `
  })
```

Now your `<super-secret-feature>` directive will only appear when the `new-feature` Feature Flag is enabled.

#### Leveraging a Feature Flag using the `FeatureFlagsService`

If you want to have more control of how you leverage a Feature Flag, you can inject the `FeatureFlagsService` into any class.

```javascript
import { FeatureFlagsService } from 'angular2-feature-flags';

@Component({ ... })
export class MyComponent {
	constructor(private ffs: FeatureFlagsService) { }
}
```

Use the `isEnabled` API on `ffs` to control behavior based on whether the Feature Flag is enabled or not.

For example, here is how you might accomplish the same functionality as the `<feature-flag>` directive using the `FeatureFlagsService`.

```html
<div *ngIf="ffs.isEnabled('new-feature')">
	Here is some content that I only want people to see if my feature flag is enabled.
</div>
```

Here is an example of you could control code execution inside your classes based on whether or not a Feature Flag is enable or disabled.

```javascript
export class MyComponent {
	constructor(private ffs: FeatureFlagsService) { }

	triggerTheThing(): void {
		if ffs.isEnabled('new-code-path') {
			console.log('Trigger the new thing');
		} else {
			console.log('Trigger the old thing');
		}
	}
}
```
