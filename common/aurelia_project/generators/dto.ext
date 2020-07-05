import { autoinject } from 'aurelia-framework';
import { Project, ProjectItem, CLIOptions, UI } from 'aurelia-cli';
import * as fse from 'fs';
import * as path from 'path';

/**
 * Represents the DTO generator
 */
@autoinject
export default class DtoGenerator {

  // tslint:disable-next-line: no-parameter-properties
  constructor(private project: Project, private options: CLIOptions, private ui: UI) {
  }

  public execute(): any {
    return this.ui
      .ensureAnswer(this.options.args[0], 'What would you like to call the new item?')
      .then((name: string) => {
        name = name.toLowerCase();
        if (name.endsWith('-dto')) {
          name = name.replace('-dto', '');
        }
        const fileName = `${this.project.makeFileName(name)}-dto`;
        const className = `${this.project.makeClassName(name)}Dto`;

        this.project.dto.add(
          ProjectItem.text(`${fileName}.ts`, this.generateSource(className))
        );

        return this.project.commitChanges()
          .then(() => {
            this.updateExports(fileName);
          })
          .then(() => this.ui.log(`Created ${fileName}.`));
      });
  }

  private updateExports(subject: string): void {

    const thePath = path.join(process.cwd(), 'src/data/dto/index.ts');
    const insertion = `export * from './${subject}';\n`;

    fse.appendFileSync(thePath, insertion);
    this.ui.log(`Exports updated for module ${subject} at ${thePath}.`);
  }

  private generateSource(className: string): string {

    // tslint:disable: no-trailing-whitespace
    // tslint:disable: align
    // tslint:disable: no-unused-expression
    // tslint:disable-next-line: semicolon
    return `/**
 *
 * Represents the data transfer object (aka DTO) for the ${className} data.
 *
 */
export class ${className} {

  public id: string = String.empty;

  public name: string = String.empty;
}`;

  }
}
