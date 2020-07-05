import { autoinject } from 'aurelia-dependency-injection';
import { ProjectItem, Project, CLIOptions, UI } from 'aurelia-cli';
import { readFileSync, existsSync } from 'aurelia-cli/dist/file-system';
import * as voca from 'voca';
import * as chalk from 'chalk';
import * as fs from 'fs';
import * as path from 'path';

/**
 * Generates simple model (entity) interface and implementation
 */
//@inject(Project, CLIOptions, UI)
@autoinject
export default class ModelGenerator {

  // tslint:disable-next-line: no-parameter-properties
  constructor(private project: Project, private options: CLIOptions, private ui: UI) { }

  public execute(): void {
    return this.ui
      .ensureAnswer(this.options.args[0], 'What would you like to call the new item?')
      .then((name: string) => {
        const fileName = this.project.makeFileName(name);
        const className = this.project.makeClassName(name);

        this.ensureInterface(fileName, className);
        this.ensureImplementation(fileName, className);
      });
  }

  private ensureInterface(fileName: string, className: string): any {
    const moduleExists: boolean = existsSync(this.getPathToItem(this.project.modelContracts.name, `${fileName}.ts`));
    const exportExists: boolean = this.isExportExists(this.project.modelContracts.name, fileName);

    if (moduleExists) {
      this.ui.log(chalk.yellow(`\nThe interface module ${fileName} exists already. \nIf you want to override this one, please delete it manually first.\n`));
      if (!exportExists) {
        this.ui.log(chalk.yellow(`\nDespite of existence of interface module ${fileName}, index.ts miss the export of. \nPlease add the export manually.`));
        this.ui.log(chalk.yellow('\nNo item created or changed.'));
      }
      return;
    }

    this.project.modelContracts.add(
      ProjectItem.text(`${fileName}.ts`, this.generateInterface(className))
    );

    return this.project.commitChanges()
      .then(() => {
        if (!(this.isExportExists(this.project.modelContracts.name, fileName))) {
          this.updateExports(this.project.modelContracts.name, fileName);
        }
      })
      .then(() => {
        this.ui.log(`Created interface I${voca.titleCase(className)}.`);
      });
  }

  private ensureImplementation(fileName: string, className: string): any {
    const moduleExists: boolean = existsSync(this.getPathToItem(this.project.modelImplementation.name, `${fileName}.ts`));
    const exportExists: boolean = this.isExportExists(this.project.modelImplementation.name, fileName);

    if (moduleExists) {
      this.ui.log(chalk.yellow(`\nThe module ${fileName} exists already. \nIf you want to override this one, please delete it manually first.\n`));
      if (!exportExists) {
        this.ui.log(chalk.yellow(`\nDespite of existence of module ${fileName}, index.ts miss the export of. \nPlease add the export manually.`));
        this.ui.log(chalk.yellow('\nNo item created or changed.'));
      }
      return;
    }

    this.project.modelImplementation.add(
      ProjectItem.text(`${fileName}.ts`, this.generateImplementation(className))
    );

    return this.project.commitChanges()
      .then(() => {
        if (!(this.isExportExists(this.project.modelImplementation.name, fileName))) {
          this.updateExports(this.project.modelImplementation.name, fileName);
        }
      })
      .then(() => {
        this.ui.log(`Created model implementation ${voca.titleCase(className)}.`);
      });
  }

  private isExportExists(dir: string, module: string): boolean {
    return readFileSync(this.getPathToItem(dir, 'index.ts')).indexOf(module) > -1;
  }

  private updateExports(where: string, subject: string): void {

    const thePath = this.getPathToItem(where, 'index.ts');
    const insertion = `export * from './${subject}';\n`;

    fs.appendFileSync(thePath, insertion);
    this.ui.log(`Exports updated for module ${subject} at ${thePath}.`);
  }

  private getPathToItem(projectItemLocation: string, item: string): string {
    return path.join(process.cwd(), this.project.root.name, projectItemLocation, item);
  }

  private generateInterface(className: any): string {

    return `import { IModel } from '@logofx/aurelia-mvvm-plugin';

/**
 *
 * Represents an interface of ${className} entity.
 *
 */
export interface I${className}  extends IModel<string> {
}`;
  }

  private generateImplementation(className: any): string {

    return `import { Model } from '@logofx/aurelia-mvvm-plugin';
import { I${className} } from 'model/contracts';

/**
 *
 * Represents the ${className} entity implementation.
 *
 */
export class ${className}  extends Model<string> implements I${className} {

  constructor() {
    super();
  }
}`;
  }

}
