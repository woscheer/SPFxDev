---
title: Use Office UI Fabric React components in your SharePoint client-side web part
description: Build a web part that uses the DocumentCard component of Office UI Fabric React.
ms.date: 06/22/2020
ms.prod: sharepoint
localization_priority: Priority
ms.custom: scenarios:getting-started
---

# Use Office UI Fabric React components in your SharePoint client-side web part

Office UI Fabric React is the front-end framework for building experiences for Office and Office 365. It includes a robust collection of responsive, mobile-first components that make it easy for you to create web experiences by using the Office Design Language.

This article describes how to build a web part like in the following image, that uses the DocumentCard component of Office UI Fabric React.

![Image of a DocumentCard Fabric component in a SharePoint workbench](../../images/fabric-components-doc-card-view-ex.png)

You can also follow these steps by watching this video on the SharePoint PnP YouTube Channel:

> [!Video https://www.youtube.com/embed/kNrYd8nYaZY]

## Office UI Fabric React & Fluent UI React

> [!NOTE]
> Microsoft renamed Office UI Fabric to Fluent UI in early 2020.

The transition from Office UI Fabric to Fluent UI, and the respective React components, is currently in a state of transition. To learn more about the current state and their roadmap, see the FAQ provided by the Fluent UI team: [FAQ Fabric and Stardust to Fluent UI](https://github.com/microsoft/fluentui/wiki/FAQ---Fabric-and-Stardust-to-Fluent-UI).

SharePoint Framework (SPFx) packages currently reference the original Office UI Fabric NPM Packages. These packages are currently supported & will continue to work.

The primary Fluent UI React package, **\@fluentui/react**, simply exports components from the **office-ui-fabric-react** package used in SharePoint Framework projects. At this time, you should continue to use the **office-ui-fabric-react** package in your SharePoint Framework projects.

This page will continue to refer to the Office UI Fabric packages until Microsoft recommends switching to the Fluent UI packages. The documentation links on this page may point to the Fluent UI documentation but it applies to the Office UI Fabric as well.

- [Fluent UI website](https://developer.microsoft.com/fluentui#/controls/web) - detailed API documentation along with implementation code examples for each control.
- [API reference](/javascript/api/office-ui-fabric-react) - detailed API reference documentation.
- [Office UI Fabric React / Fluent UI React](https://github.com/microsoft/fluentui) - source code repository on github.

## Create a new web part project

1. Create a new project directory in your favorite location:

    ```console
    md documentcardexample-webpart
    ```

1. Go to the project directory:

    ```console
    cd documentcardexample-webpart
    ```

1. Make sure you have the latest version of **\@microsoft/generator-sharepoint** installed and create a new web part by running the Yeoman SharePoint generator:

    ```console
    yo @microsoft/sharepoint
    ```

1. When prompted, enter the following values (*select the default option for all prompts omitted below*):

    - **What is your solution name?**: documentcardexample-webpart
    - **Which baseline packages do you want to target for your component(s)?**: SharePoint Online only (latest)
    - **Where do you want to place the files?**: Use the current folder
    - **Do you want to allow the tenant admin the choice of being able to deploy the solution to all sites immediately without running any feature deployment or adding apps in sites?**: Yes
    - **Will the components in the solution require permissions to access web APIs that are unique and not shared with other components in the tenant?**: No
    - **Which type of client-side component to create?**: Web Part
    - **What is your Web part name?**: DocumentCardExample
    - **What is your Web part description?**: DocumentCardExample description
    - **Which framework would you like to use?**: React

    At this point, Yeoman installs the required dependencies and scaffolds the solution files.

    Starting with SPFx v1.8.2 version, Yeoman will include the recommended **\@microsoft/sp-office-ui-fabric-core** package version to your solution when you select the **React** as the desired web framework.

    > [!NOTE]
    > Starting with SPFx v1.8, you can use either Office UI Fabric React v5 or v6. Each version of SPFx upgrades the version of Office UI Fabric React in new projects. For example:
    >
    > - SPFx v1.8 projects use Office UI Fabric React v5.132.0 by default
    > - SPFx v1.9 & v1.10 projects use Office UI Fabric React v6.189.2 by default
    >
    > Different versions of Office UI Fabric React have different dependencies on versions of TypeScript. If you want to upgrade an existing SPFx project to a newer version of Office UI Fabric React, see the following section: [Appendix: Upgrading Office UI Fabric React versions](#appendix-upgrading-office-ui-fabric-react-versions)
    >
    > This page assumes you are using the latest version of the SharePoint Framework.

1. Next, execute the following command to open the web part project in Visual Studio Code:

    ```console
    code .
    ```

    You now have a web part project with the React web framework.

1. Open **DocumentCardExampleWebPart.ts** from the **src\webparts\documentCardExample** folder.

    As you can see, the `render()` method creates a react element and renders it in the web part DOM.

    ```typescript
    public render(): void {
      const element: React.ReactElement<IDocumentCardExampleProps> = React.createElement(
        DocumentCardExample,
        {
          description: this.properties.description
        }
      );

      ReactDom.render(element, this.domElement);
    }
    ```

1. Open **DocumentCardExample.tsx** from the **src\webparts\documentCardExample\components** folder.

    This is the main react component that Yeoman added to your project that renders in the web part DOM.

    ```tsx
    export default class DocumentCardExample extends React.Component<IDocumentCardExampleProps, {}> {
      public render(): React.ReactElement<IDocumentCardExampleProps> {
        return (
          <div className={ styles.documentCardExample }>
            <div className={ styles.container }>
              <div className={ styles.row }>
                <div className={ styles.column }>
                  <span className={ styles.title }>Welcome to SharePoint!</span>
                  <p className={ styles.subTitle }>Customize SharePoint experiences using web parts.</p>
                  <p className={ styles.description }>{escape(this.props.description)}</p>
                  <a href="https://aka.ms/spfx" className={ styles.button }>
                    <span className={ styles.label }>Learn more</span>
                  </a>
                </div>
              </div>
            </div>
          </div>
        );
      }
    }
    ```

## Add an Office UI Fabric component

The *new modern experiences* in SharePoint use Office UI Fabric and Office UI Fabric React as the default front-end framework for building the new experiences. As a result, SharePoint Framework ships with a default version of Office UI Fabric and Fabric React that matches the version available in SharePoint. This ensures that the web part you're building uses the right version of the Fabric styles and components when deployed to SharePoint.

Because we chose React as our framework when creating the solution, the generator installed the right version of Office UI Fabric React as well. You can directly import the Fabric components in your react components without any additional work.

### To add an Office UI Fabric component

1. Open **DocumentCardExample.tsx** from the **src\webparts\documentCardExample\components** folder.

1. Add the following `import` statement to the top of the file to import Fabric React components that we want to use.

    ```typescript
    import {
      DocumentCard,
      DocumentCardPreview,
      DocumentCardTitle,
      DocumentCardActivity,
      IDocumentCardPreviewProps
    } from 'office-ui-fabric-react/lib/DocumentCard';
    ```

1. Replace the existing `render()` method with the following:

    ```typescript
    public render(): JSX.Element {
      const previewProps: IDocumentCardPreviewProps = {
        previewImages: [
          {
            previewImageSrc: String(require('./document-preview.png')),
            iconSrc: String(require('./icon-ppt.png')),
            width: 318,
            height: 196,
            accentColor: '#ce4b1f'
          }
        ],
      };

      return (
        <DocumentCard onClickHref='http://bing.com'>
          <DocumentCardPreview { ...previewProps } />
          <DocumentCardTitle title='Revenue stream proposal fiscal year 2016 version02.pptx' />
          <DocumentCardActivity
            activity='Created Feb 23, 2016'
            people={
              [
                { name: 'Kat Larrson', profileImageSrc: String(require('./avatar-kat.png')) }
              ]
            }
          />
        </DocumentCard>
      );
    }
    ```

1. Save the file.

    In this code, the `DocumentCard` component includes some extra sections:

    - DocumentCardPreview
    - DocumentCardTitle
    - DocumentCardActivity

    The `previewProps` property includes some properties of the **DocumentCardPreview** component.

## Copy the image assets

Copy the following images to your **src\webparts\documentCardExample\components** folder:

- [avatar-kat.png](https://github.com/SharePoint/sp-dev-docs/blob/master/assets/avatar-kat.png)
- [icon-ppt.png](https://github.com/SharePoint/sp-dev-docs/tree/master/assets/icon-ppt.png)
- [document-preview.png](https://github.com/SharePoint/sp-dev-docs/tree/master/assets/document-preview.png)

> [!NOTE]
> In the code, we were referencing these images using relative path from the root location. As your reference the images in the code, it will cause them to be included in the **\*.sppkg** solution package as long as you have the `includeClientSideAssets` set as `true` in the **package-solution.json** file.

## Preview the web part in workbench

1. In the console, enter the following to preview your web part in workbench:

    ```console
    gulp serve
    ```

1. In the toolbox, select your `DocumentCardExample` web part to add:

    ![Image of a DocumentCard Fabric component in a SharePoint workbench](../../images/fabric-components-doc-card-view-ex.png)