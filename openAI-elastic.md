1. Backend - Spring Boot
Configurer le projet Spring Boot
Créez un projet Spring Boot avec les dépendances suivantes dans pom.xml :

xml
Copier le code
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.pdfbox</groupId>
        <artifactId>pdfbox</artifactId>
        <version>2.0.27</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
</dependencies>
Configurer Elasticsearch
Ajoutez les paramètres de connexion dans application.properties :

properties
Copier le code
spring.elasticsearch.uris=http://localhost:9200
spring.elasticsearch.username=your_username
spring.elasticsearch.password=your_password
spring.jpa.hibernate.ddl-auto=update
Entité pour les documents multimodaux
Créez une classe pour représenter les documents indexés :

java
Copier le code
import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;

@Document(indexName = "documents")
public class Document {
    @Id
    private String id;
    private String title;
    private String content;
    private String pdfPath;
    private String imagePath;

    // Getters and setters
}
Service pour gérer les documents
Incluez les fonctionnalités pour lire les PDF et indexer les données dans Elasticsearch :

java
Copier le code
import org.apache.pdfbox.pdmodel.PDDocument;
import org.apache.pdfbox.text.PDFTextStripper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.io.File;
import java.io.IOException;

@Service
public class DocumentService {

    @Autowired
    private DocumentRepository documentRepository;

    public String extractTextFromPdf(String pdfPath) throws IOException {
        try (PDDocument document = PDDocument.load(new File(pdfPath))) {
            PDFTextStripper pdfStripper = new PDFTextStripper();
            return pdfStripper.getText(document);
        }
    }

    public void indexDocument(String title, String pdfPath, String imagePath) throws IOException {
        String content = extractTextFromPdf(pdfPath);

        Document doc = new Document();
        doc.setTitle(title);
        doc.setContent(content);
        doc.setPdfPath(pdfPath);
        doc.setImagePath(imagePath);

        documentRepository.save(doc);
    }

    public Iterable<Document> searchDocuments(String query) {
        return documentRepository.findByContentContaining(query);
    }
}
Contrôleur REST
Exposez des points d'API pour télécharger et rechercher des documents :

java
Copier le code
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.File;
import java.io.IOException;

@RestController
@RequestMapping("/api/documents")
public class DocumentController {

    @Autowired
    private DocumentService documentService;

    @PostMapping("/upload")
    public String uploadDocument(@RequestParam String title,
                                 @RequestParam("pdf") MultipartFile pdf,
                                 @RequestParam("image") MultipartFile image) throws IOException {
        String pdfPath = "uploads/" + pdf.getOriginalFilename();
        String imagePath = "uploads/" + image.getOriginalFilename();
        pdf.transferTo(new File(pdfPath));
        image.transferTo(new File(imagePath));

        documentService.indexDocument(title, pdfPath, imagePath);
        return "Document indexed successfully!";
    }

    @GetMapping("/search")
    public Iterable<Document> searchDocuments(@RequestParam String query) {
        return documentService.searchDocuments(query);
    }
}
2. Frontend - Angular
Configurer Angular
Créez un projet Angular :

bash
Copier le code
ng new angular-documents
cd angular-documents
npm install @angular/material
Service Angular
Créez un service pour interagir avec l'API backend :

typescript
Copier le code
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class DocumentService {
  private apiUrl = 'http://localhost:8080/api/documents';

  constructor(private http: HttpClient) {}

  uploadDocument(formData: FormData): Observable<any> {
    return this.http.post(`${this.apiUrl}/upload`, formData);
  }

  searchDocuments(query: string): Observable<any> {
    return this.http.get(`${this.apiUrl}/search?query=${query}`);
  }
}
Composant Angular
Créez un composant pour uploader et rechercher des documents :

typescript
Copier le code
import { Component } from '@angular/core';
import { DocumentService } from './document.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  selectedPdf: File | null = null;
  selectedImage: File | null = null;
  searchResults: any[] = [];

  constructor(private documentService: DocumentService) {}

  onFileSelected(event: any, type: string): void {
    if (type === 'pdf') {
      this.selectedPdf = event.target.files[0];
    } else if (type === 'image') {
      this.selectedImage = event.target.files[0];
    }
  }

  uploadDocument(): void {
    if (this.selectedPdf && this.selectedImage) {
      const formData = new FormData();
      formData.append('title', 'Example Document');
      formData.append('pdf', this.selectedPdf);
      formData.append('image', this.selectedImage);

      this.documentService.uploadDocument(formData).subscribe((response) => {
        console.log(response);
      });
    }
  }

  searchDocuments(query: string): void {
    this.documentService.searchDocuments(query).subscribe((results) => {
      this.searchResults = results;
    });
  }
}
Template Angular
Créez un template simple pour gérer l'upload et la recherche :

html
Copier le code
<div>
  <h1>Document Management</h1>

  <h2>Upload Document</h2>
  <input type="file" (change)="onFileSelected($event, 'pdf')" placeholder="Upload PDF" />
  <input type="file" (change)="onFileSelected($event, 'image')" placeholder="Upload Image" />
  <button (click)="uploadDocument()">Upload</button>

  <h2>Search Documents</h2>
  <input type="text" #query placeholder="Enter search query" />
  <button (click)="searchDocuments(query.value)">Search</button>

  <div *ngIf="searchResults.length > 0">
    <h3>Results:</h3>
    <ul>
      <li *ngFor="let result of searchResults">
        {{ result.title }} - {{ result.content }}
      </li>
    </ul>
  </div>
</div>
3. Lancer l'application
Démarrer Elasticsearch sur localhost:9200.
Démarrer Spring Boot avec :
bash
Copier le code
mvn spring-boot:run
Démarrer Angular avec :
bash
Copier le code
ng serve
Conclusion
Les documents (texte, PDF, images) sont stockés et indexés dans Elasticsearch.
Angular permet de télécharger les documents et d'afficher les résultats de recherche.
Cette solution peut être enrichie avec des fonctionnalités comme la pagination, des filtres avancés, ou une visualisation des images et PDF dans le frontend. 😊










ChatGPT peut faire des erreurs. Envisagez de vérifier les informations importantes.
